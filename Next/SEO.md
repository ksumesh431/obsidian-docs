Use metadata and also use script 

``` ts
import type { Metadata } from "next";

import { Geist, Geist_Mono } from "next/font/google";

import "./globals.css";

import "@radix-ui/themes/styles.css";

import { Theme } from "@radix-ui/themes";

import CustomNav from "@/components/custom-nav";

import CustomFooter from "@/components/footer";

const geistSans = Geist({

variable: "--font-geist-sans",

subsets: ["latin"],

});

  

const geistMono = Geist_Mono({

variable: "--font-geist-mono",

subsets: ["latin"],

});

  
  

export const metadata: Metadata = {

title: "Docoaid",

description: "Your trusted partner in document authentication and legalization. We specialize in document verification, notary services, translations, and legal documentation. With years of expertise, we provide reliable, efficient, and secure services for individuals and businesses, ensuring your documents meet all necessary standards and requirements.",

openGraph: {

title: "Docoaid - Document Authentication & Legalization",

description: "Reliable document verification, notary services, and translations. Trusted by individuals & businesses worldwide.",

url: "https://docoaid.com",

siteName: "Docoaid",

images: [

{

url: "https://docoaid.com/logo.webp",

width: 800,

height: 600,

alt: "Docoaid Logo",

},

],

locale: "en_US",

type: "website",

},

twitter: {

card: "summary_large_image",

title: "Docoaid",

description: "Your trusted partner in document authentication and legalization.",

images: ["https://docoaid.com/logo.webp"],

},

};

  

export default function RootLayout({

children,

}: Readonly<{

children: React.ReactNode;

}>) {

return (

<html lang="en" className="h-full no-scrollbar">

<body className={`${geistSans.variable} ${geistMono.variable} antialiased flex flex-col min-h-screen bg-[#f9fafb]`}>

{/* Inject JSON-LD for SEO */}

<script

type="application/ld+json"

dangerouslySetInnerHTML={{

__html: JSON.stringify({

"@context": "https://schema.org",

"@type": "Organization",

"name": "Docoaid",

"url": "https://docoaid.com",

"logo": "https://docoaid.com/logo.webp",

"description":

"Docoaid is a platform that provides document authentication, verification, and legal services.",

"sameAs": [

"https://www.facebook.com/docoaid",

"https://twitter.com/docoaid",

"https://www.linkedin.com/company/docoaid",

],

"brand": {

"@type": "Brand",

"name": "Docoaid"

}

}),

}}

/>

<div className="contents desktop-zoom">

<Theme className="contents"> {/* Using contents to prevent Theme from creating its own box */}

<CustomNav />

<div >{children}</div>

</Theme>

</div>

<CustomFooter />

</body>

</html>

);

}

```


- Resgister and index page in google search console
- install next-sitemap and use it to generate site maps
- put sitemaps in google search console
- set up page redirects rule in cloudflare, that is www.docoaid.com should redirect to docoaid.com