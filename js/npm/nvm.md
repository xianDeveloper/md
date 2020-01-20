# NVM安装

## window

- nvm程序安装

  nvm windows 最新下载地址

  ```bash
  https://github.com/coreybutler/nvm-windows/releases
  ```

  ### 安装之前的操作

  **请注意**： 在安装nvm for windows之前，你需要卸载任何现有版本的node.js。并且需要删除现有的nodejs安装目录（例如："C:\Program Files\nodejs’）。因为，nvm生成的symlink（符号链接/超链接)不会覆盖现有的（甚至是空的）安装目录。
  你还需要删除现有的npm安装位置（例如“C:\Users\weiqinl\AppData\Roaming\npm”），以便正确使用nvm安装位置。

- nvm配置

  配置下列url，提高下载安装速度

  ```bash
  nvm node_mirror "https://npm.taobao.org/mirrors/node/"
  nvm npm_mirror "https://npm.taobao.org/mirrors/npm/"
  ```

- 通过nvm安装nodejs

  ```bash
  nvm ls   // 查看目前已经安装的版本
  nvm install 6.10.0  // 安装指定的版本的nodejs
  nvm use 6.10.0  // 使用指定版本的nodejs
  ```

- nvm常用命令

  ```bash
  nvm arch [32|64] ： 显示node是运行在32位还是64位模式。指定32或64来覆盖默认体系结构。
  nvm install <version> [arch]： 该<version>可以是node.js版本或最新稳定版本latest。（可选[arch]）指定安装32位或64位版本（默认为系统arch）。设置[arch]为all以安装32和64位版本。在命令后面添加--insecure ，可以绕过远端下载服务器的SSL验证。
  nvm list [available]： 列出已经安装的node.js版本。可选的available，显示可下载版本的部分列表。这个命令可以简写为nvm ls [available]。
  nvm on： 启用node.js版本管理。
  nvm off： 禁用node.js版本管理(不卸载任何东西)
  nvm proxy [url]： 设置用于下载的代理。留[url]空白，以查看当前的代理。设置[url]为none删除代理。
  nvm node_mirror [url]：设置node镜像，默认为https://nodejs.org/dist/.。我建议设置为淘宝的镜像https://npm.taobao.org/mirrors/node/
  nvm npm_mirror [url]：设置npm镜像，默认为https://github.com/npm/npm/archive/。我建议设置为淘宝的镜像https://npm.taobao.org/mirrors/npm/
  nvm uninstall <version>： 卸载指定版本的nodejs。
  nvm use [version] [arch]： 切换到使用指定的nodejs版本。可以指定32/64位[arch]。nvm use <arch>将继续使用所选版本，但根据提供的值切换到32/64位模式的<arch>
  nvm root [path]： 设置 nvm 存储node.js不同版本的目录 ,如果<path>未设置，将使用当前目录。
  nvm version： 显示当前运行的nvm版本，可以简写为nvm v
  
  作者：weiqinl
  链接：https://www.jianshu.com/p/324044f2f542
  来源：简书
  著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
  ```

  