##nginx安装

>nginx版本为1.14.1

#### vim支持nginx语法高亮

- cp -r  contrib/vim/* ~/.vim/，如果不存在~/.vim则创建

- vim /usr/share/vim/vim74/filetypr.vim ,在文件中添加

  ```
  au BufRead,BufNewFile /usr/local/nginx/conf/* set ft=nginx
  ```

- 效果如图

 ![nginx](https://user-images.githubusercontent.com/13096375/49052433-c013c400-f226-11e8-8554-d35df68e410f.jpg)




