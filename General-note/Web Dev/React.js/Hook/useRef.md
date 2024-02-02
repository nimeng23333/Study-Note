
useRef做页面滚动

```js
export default function Home() {
  const pageRef = useRef(null);
  const scrollToSurvey = () => {
    const top = pageRef.current.getBoundingClientRect().top + window.pageYOffset - 72;
    window.scrollTo({ top, behavior: 'smooth' });
  }
  const scrollToTop = () => {
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }
  return(
    <div className="overflow-y-hidden text-center">
      <Hero onClick={scrollToSurvey}/>
      <Introduction/>
      <Survey sectionRef={pageRef}/>
      <Comparison/>
      <FontAwesomeIcon className="text-3xl text-primary cursor-pointer" icon={faChevronCircleUp} onClick={scrollToTop}/>
    </div>
    );
}
```

### hash地址跳转 （#anchor）

a href="#id"

section id="id"

正常情况是立刻跳转，要用滚动事件需要先preventDefault


### useRef做点击外部检测

将nav作为ref，监听是否点击到target

```js
export const NavBar = ()=>{
    const [activeSection, setActiveSection] = useState();
    const [navShow,setNavShow] = useState(false);
    const navRef = useRef();
    useEffect(() =>{

      function handleClickOutside(event) {
        if (navRef.current && !navRef.current.contains(event.target)) {
          setNavShow(false);
        }
      }
      // 在组件挂载后添加点击事件监听器
      document.addEventListener('mousedown', handleClickOutside);
      return () => {
        // 在组件卸载后移除事件监听器
        document.removeEventListener('mousedown', handleClickOutside);
      };
    },[navRef])

    useEffect(() => {
        const handleScroll = () => {
            const sections = document.getElementsByTagName("section");
            const documentHeight = document.documentElement.scrollHeight;
            const sectionArray = [];
            Array.from(sections).map((section) =>{
                const sectionTop = section.getBoundingClientRect().top + window.pageYOffset - 200;
                sectionArray.push({top:sectionTop,id:section.id});
            });
            for(let i=sectionArray.length;i--;i>0){
              if(window.pageYOffset>sectionArray[i].top){
                setActiveSection(sectionArray[i].id);
                break;
              }
            }
        };
        window.addEventListener('scroll', handleScroll);
        return () => {
          window.removeEventListener('scroll', handleScroll);
        };
    }, []);

    const handleClick = (event) => {
        event.preventDefault();
        const hash = event.target.hash.replace('#', '');
        const element = document.getElementById(hash);
        if (element) {
          const top = element.getBoundingClientRect().top + window.pageYOffset - 72;
          window.scrollTo({ top, behavior: 'smooth' });
          setActiveSection(hash);
        }
      };

    const handleCollaps = () =>{
      setNavShow(!navShow);
    }
    return(
        <nav ref={navRef} className={`bg-white py-5 fixed top-0 z-50 w-full shadow-md flex justify-between navContainer transition-all duration-500 ${navShow? "max-h-[300px]":"max-h-[72px]"}`}>
          <Logo href="/"></Logo>
          <ul className="ml-auto flex max-md:flex-col items-end">
            <li className="block md:hidden">
              <button className="font-light text-lg cursor-pointer mx-3 text-primary" onClick={handleCollaps}>
                  <FontAwesomeIcon icon={faBars} />
              </button>
            </li>
              <NavLink href="#introduction" onClick={handleClick} isActive={activeSection =="introduction"} navShow={navShow}>Introduction</NavLink>
              <NavLink href="#survey" onClick={handleClick} isActive={activeSection == "survey"} navShow={navShow}>Quick Survey</NavLink>
              <NavLink href="#compare" onClick={handleClick} isActive={activeSection == "compare"} navShow={navShow}>Comparison</NavLink>
          </ul>
        </nav>
    )
}
```

