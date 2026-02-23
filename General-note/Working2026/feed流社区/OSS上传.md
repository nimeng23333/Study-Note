---
title: OSS上传
---
## 核心上传包：`packages/ext-oss`

1. ### 主要上传函数
    

核心上传实现在 packages/ext-oss/src/single-upload.js：

```TypeScript
async function singleUpload(bizCode, uuid, file, communityType, isSDTmp, onCalcHashEnd) {
  const methodName = 'singleUpload';
  try {
    // ... 参数校验 ...
    
    // hash
    const fileHash = await calculateHash(file, onCalcHashEnd);
    logger.info(methodName, 'fileHash', fileHash);
    if (!fileHash) return;
    // client
    const client = await getOssClient(bizCode, isSDTmp);
    if (!client) return;
    // filename
    let fileName = file.name;
    const fileExtension = fileName.substring(fileName.lastIndexOf('.'));
    if (fileExtension && fileHash) fileName = fileHash + fileExtension;
    logger.info(methodName, 'fileName', fileName);
    // oss path
    let preOssPath;
    if (bizCode === '0') {
      if (communityType == 'video') {
        preOssPath = 'video';
      } else {
        preOssPath = 'img';
      }
    }
    if (bizCode === '1') {
      preOssPath = 'web';
      if (communityType == 'image') {
        preOssPath = 'web/image';
      } else if (communityType == 'other') {
        preOssPath = 'web';
      }
    }
    if (bizCode === '2') {
      preOssPath = 'train_img';
    }
    const ossPath = ${preOssPath}/${uuid}/${fileName};
    logger.info(methodName, 'ossPath', ossPath);
    // put
    await client.put(ossPath, file);
    // return
    const url = ${isSDTmp ? config.cdnHostForSDTmp : config.cdnHost}/${ossPath};
    return { file: ossPath, url: url };
  } catch (error) {
    logger.error(methodName, 'get oss token error');
    logger.error(methodName, error);
  }
}
```

  

2. ### 常用的上传函数接口
    

从 single-upload.js 导出的函数：

```TypeScript
export const singleUploadForSD = async (uuid, file, onCalcHashEnd) => {
  return await singleUpload('0', uuid, file, null, null, onCalcHashEnd);
};
/**
上传-生图（bizCode为0）-tmp
 * @param {*} uuid
 * @param {*} file
 * @param {*} onCalcHashEnd
 * @returns
 */
export const singleUploadForSDTmp = async (uuid, file, onCalcHashEnd) => {
  return await singleUpload('0', uuid, file, null, 'tmp', onCalcHashEnd);
};
// ... 其他上传函数 ...
返回格式：
{
  file: string;  // OSS 路径，如 "img/uuid/filename.jpg"
  url: string;   // CDN 访问链接，如 "https://cdn.example.com/img/uuid/filename.jpg"
}
```

  

3. ### liblib 中的使用示例
    

#### 示例 1：图片上传（带审核）

```TypeScript
export const postUploadData = async (
  userUUID: string | undefined,
  file: File,
  skipVerify = false,
  onUploadProcess = (process: ImageUploadProcessEnum) => {},
): Promise<PostUploadDataResult> => {
  // 回调图片上传中
  onUploadProcess(ImageUploadProcessEnum.IMAGE_UPLOADING);
  const result = (await singleUploadForSD(userUUID, file)) as SingleUploadResult;
  // 回调图片上传成功
  onUploadProcess(ImageUploadProcessEnum.IMAGE_UPLOAD_SUCCESS);
  if (!skipVerify) {
    return {
      ...result,
      isValid: true,
    };
  }
  if (!result?.url) {
    // 回调图片OSS验证成功
    onUploadProcess(ImageUploadProcessEnum.IMAGE_UPLOAD_FAILED);
    // @ts-ignore 正常情况不会这样，为了避免外部的改动，这里先隐藏提示
    return {
      isValid: false,
    };
  }
  // const isUnvalid = await ossService.getImageOssReview({
  //   url: result.url,
  //   type: 'community',
  // });
  const isInvalid = await ossService.getImageOssReview();
  // 回调图片OSS验证成功
  onUploadProcess(ImageUploadProcessEnum.IMAGE_OSS_VERIFY_SUCCESS);
  if (isInvalid) {
    return {
      ...result,
      isValid: false,
    };
  } else {
    if (checkMediaType(result.url) === 'video') {
      return {
        ...result,
        isValid: true,
      };
    }
    const verifyResult = await imageVerify([result.url]);
    onUploadProcess(ImageUploadProcessEnum.IMAGE_QUALITY_VERIFY_SUCCESS);
    if (verifyResult && verifyResult.length > 0) {
      const isQualified = verifyResult[0].result;
      return {
        ...result,
        isValid: !isQualified, // false代表通过
      };
    } else {
      return {
        ...result,
        isValid: false,
      };
    }
  }
};
```

  

#### 示例 2：AI 工具中的图片上传

```TypeScript
export const uploadImage = async (file: File, toTempBucket = false, from = 'default', isVerify = true) => {
  const { uuid } = (await getUserinfo()) || {};
  // 如果获取到用户的uuid为空,提示刷新页面更新登录态
  if (!uuid) {
    return message.error('图片上传失败,当前登录已经失效,请刷新页面重试');
  }
  try {
    const temp = await (toTempBucket ? singleUploadForSDTmp : singleUploadForSD)(uuid, file, null);
    if (!temp || !temp.url) {
      message.error('图片上传失败, 调用上传sdk失败, 请重试!');
      return null;
    }
    // 接入机审
    if (isVerify) {
      const verifyResult = await imageVerify([temp.url]);
      if (verifyResult && verifyResult.length > 0) {
        const isQualified = verifyResult[0].result;
        if (!isQualified) {
          message.error('图片违规，请上传其他图片');
          return null;
        }
        return {
          ...temp,
          isValid: !isQualified, // false代表通过
        };
      } else {
        message.error('图片违规，请上传其他图片');
        return null;
      }
    } else {
      return temp;
    }
  } catch (error) {
    message.error('图片上传失败, 请重试!');
    return null;
  }
};
```

示例 3：文件上传（支持进度回调）

```TypeScript
export const uploadFile = async (uuid, file, onUploadProgress) => {
  const methodName = 'file upload';
  try {
    // log
    logger.info(methodName, 'file', file);
    if (!file) {
      logger.info(methodName, 'need file');
      return;
    }
    // client
    const bizCode = '1';
    const client = await getOssClient(bizCode);
    if (!client) return;
    // filename
    let fileName = file.name;
    const fileExtension = fileName.substring(fileName.lastIndexOf('.'));
    if (fileExtension) fileName = UUIDv4() + fileExtension.toLowerCase();
    logger.info(methodName, 'fileName', fileName);
    // oss path
    const preOssPath = 'web';
    const ossPath = ${preOssPath}/${uuid}/${fileName};
    logger.info(methodName, 'ossPath', ossPath);
    // part size from url
    let partSize = config.modelUploadPartSize;
    const partSizeSearch = new URLSearchParams(location.search).get('partsize');
    if (partSizeSearch) {
      const partSizeInt = parseInt(partSizeSearch);
      if (partSizeInt > 0 && partSizeInt <= 20) partSize = partSizeInt * 1024 * 1024;
    }
    logger.info(methodName, 'partSizeSearch', partSizeSearch);
    logger.info(methodName, 'partSize', partSize);
    // multipart upload
    const multipartOptions = {
      timeout: config.modelUploadTimeout,
      parallel: config.modelUploadParallel,
      partSize: partSize,
      progress: (percentage, checkpoint, res) => {
        if (onUploadProgress) onUploadProgress(percentage, checkpoint, res);
      },
    };
    logger.info(methodName, 'multipartOptions', multipartOptions);
    // upload res
    const multipartRes = await client.multipartUpload(ossPath, file, multipartOptions);
    logger.info(methodName, 'multipartRes', multipartRes);
    if (!multipartRes || !multipartRes.res || multipartRes.res.status !== 200) {
      logger.error(methodName, 'model upload fail');
      return;
    }
    // return
    const url = ${config.cdnHost}/${ossPath};
    const finalRes = {
      bucket: multipartRes.bucket,
      etag: multipartRes.etag,
      file: ossPath,
      url: url,
    };
    logger.info(methodName, 'finalRes', finalRes);
    return finalRes;
  } catch (error) {
    logger.error(methodName, 'model upload error');
    logger.error(methodName, error);
  }
};
```

  

使用示例：

```TypeScript
customRequest={async (options) => {
          setShowFileItemBar(true);
          setUploading(true);
          console.log('=====options.file:', options.file);
          const res = await uploadFile(userUUID, options.file, (percent: number) => {
            setFileUploadProgress({
              fileName: options.file?.name || '',
              progress: percent * 100,
            });
          });
          console.log('===上传结果：', res);
          message.destroy((options.file as any)?.uid);
          if (!res) {
            setUploading(false);
            return message.error(${(options.file as any).name}上传失败);
          }
          // 更新名字为文件真实名称
          res.file = (options.file as any)?.name;
          options.onSuccess?.({
            ...res,
          });
          // 保存图片记录到IndexedDB
          try {
            await imageDB.saveImage({
              url: res.url,
              fileName: (options.file as any)?.name || 'unknown',
              fileSize: (options.file as any)?.size || 0,
              fileType: (options.file as any)?.type || 'unknown',
            });
          } catch (error) {
            console.warn('Failed to save image record to IndexedDB:', error);
          }
          onSuccess(res.url);
          setUploading(false);
        }}
```

总结

1. 核心包：@liblibaix/ext-oss
    
2. 主要函数：
    

- singleUploadForSD(uuid, file, onCalcHashEnd) - 生图业务
    
- singleUploadForSDTmp(uuid, file, onCalcHashEnd) - 生图临时
    
- uploadFile(uuid, file, onUploadProgress) - 通用文件上传（支持进度）
    

1. 返回值：
    

- file: OSS 路径（如 img/uuid/filename.jpg）
    
- url: CDN 访问链接（如 https://cdn.example.com/img/uuid/filename.jpg）
    

1. OSS 客户端获取：通过 getOssClient 从后端获取 STS 临时凭证，使用 ali-oss SDK 上传