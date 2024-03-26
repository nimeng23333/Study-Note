---
title: Next.js - 字体
---

## fontawsome
```jsx
import { FontAwesomeIcon } from "@fortawesome/react-fontawesome";
import { faCoins } from "@fortawesome/free-solid-svg-icons";

 <FontAwesomeIcon icon={faCoins} className="text-yellow-500" />
```

## google字体
```jsx
import {DM_Sans, DM_Serif_Display} from '@next/font/google';

const dmSans = DM_Sans({
  weight:["400","500","700"],
  subsets:["latin"],   //取子集，一个字体包很大，并不需要全部东西
  variable:"--font-dm-sans", //变量名
})

const dmSerif = DM_Serif_Display({
  weight:["400"],
  subsets:["latin"],
  variable:"--font-dm-serif",
})

function MyApp({ Component, pageProps }) {
  const getLayout = Component.getLayout || ((page) =>page) 
  return (
    <UserProvider>
    <main className={`${dmSans.variable} ${dmSerif.variable}` /*在tailwind下字体通过加一个main，将字体的variable赋给className */} >   
      {getLayout(<Component {...pageProps} />, pageProps) /* getLayout有两个参数,一个page一个props，这里的page也就是component */}  
    </main>
    </UserProvider>
  )
}
```
然后需要在tailwind.config.js里进行字体配置
```jsx
theme: {
	extend:{
	  fontFamily:{
		body:"var(--font-dm-sans)",
		heading:"var(--font-dm-serif)",
	  }
	}
}
```
然后就可以作为className使用了
`className="font-heading"`
`className="font-body"`