# vs code

### 扩展插件
#### 必备
- VS Live Share  
  可以多人合作编程，神器；[地址](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)
  
- Live Server  
  只要一键就可以起一个本地web服务器，很方便；[地址](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)

- GitLens — Git supercharged  
  git超级版，可以显示代码的每一行git提交记录；[地址](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
  
- Settings Sync  
  利用github同步vscode的设置；[地址](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

### 坚决不用
- Auto-Open Markdown Preview [地址](https://marketplace.visualstudio.com/items?itemName=hnw.vscode-auto-open-markdown-preview)
  原因：用了之后，提交到github之后，会出现红色点，在github上显示为**?**，生成网站时具体的html如下：
  ``` html
  <span class="cm-invalidchar" title="\u8" aria-label="\u8" cm-text="">•</span>
  ```
  应该是这个插件在某种情况下会自动添加这个符号，已经反馈到插件的issue上了。 
  [issue反馈地址](https://github.com/hnw/vscode-auto-open-markdown-preview/issues/19)
  更新于2018/9/27:
  出现这个特殊的符号，应该是因为vscode的markdown预览自带的问题。[vscode反馈地址](https://github.com/Microsoft/vscode/issues/59521)