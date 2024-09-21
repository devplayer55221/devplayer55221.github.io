While installing it in Windows, follow these links:
- [Hugo installation](https://gohugo.io/installation/windows/)
  - Install Git, Go
- [GCC installation](https://discourse.gohugo.io/t/gcc-compiler-required-to-build-hugo-from-source-on-windows/41370)
- `hugo` command can be used to build
- [No layout error](https://discourse.gohugo.io/t/found-no-layout-file-for-html/18983/3) - Need to download the template
  - [Git clone the template](https://github.com/rhazdon/hugo-theme-hello-friend-ng/tree/51e697bea7eb265c5b6bc532636bb4c707a84173) - Put the contents in the 'template folder' already present inside the 'templates' folder
- [Google analytics deprecation error](https://discourse.gohugo.io/t/deprecation-of-site-googleanalytics-in-v0-120-0/46879)
  - Need to change the statemnet as mentioned in the link. Path - "<template folder>/layouts/partial/<file>"
- `hugo server` command should work
