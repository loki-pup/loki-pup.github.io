In the parent, you don’t pass the argument because you're just giving the child the function to use.

In the child, you decide when and how to call that function — so you provide the argument there.


responsive: by changing the text size and column width for different platform

* flex: Utilities for controlling how flex items both grow and shrink.

* h-screen: Use h-screen to make an element span the entire height of the viewport.

* Flex Direction: Utilities for controlling the direction of flex items

* px-0:	padding-left: 0px; padding-right

* container mx-auto: To center a container

* mb-0:	margin-bottom: 0px


## code

### page 

```
  import HeroSection from "./components/HeroSection";
  
  export default function Home(){
     return (
        <main className = "flex min-h-screen flex-col bg-[#121212] container mx-auto px-12  py-4">
            <HeroSection /> 
        </main>
     );
  }
```


### hero section

```
import React from "react";
import Image from "next/image";

const HeroSection = () => {
    return (
        <section>
            <div className="grid grid-cols-1 lg:grid-cols-12">
                <div className = "col-span-7 place-self-center">
                <h1 className="text-white mb-4 text-4xl lg:text-6xl font-extrabold ">Hello, I'm Lulu</h1>
                <p className="text-[#ADB7BE] text-lg lg:text-xl">
                    Bozyu is a border collie
                </p>
                </div>
                <div className="col-span-5">
                    <div className="rounded-full bg-[#181818] w-[500px] h-[500px] relative">
                    <Image
                    src = "/image/bozyu.png"
                    alt = "bozyu image"
                    className = "absolute transform -translate-x-1/2 -translate-y-1/2 top-1/2 left-1/2"
                    width = {300}
                    height= {300}
                    />
                    </div>
                </div>
            </div>
        </section>
    );
};

export default HeroSection; 
```
