
# 设置用户信息

```bash
git config --global user.name "hahaha"
git config --global user.email "1234567@123.com"
```

# 为常用的指令配置别名

在用户目录创建 `.bashrc`  文件
```bash
touch ~/.bashrc   # 这里的 ~ 表示用户目录
```

在其中输入内容
```
# 基础格式：
# alias 别名='原指令'

# 例1：用于输出git提交日志
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'

# 例2：用于输出当前目录所有文件及基本信息
alias ll='ls -al'
```

打开gitBash，执行 `source ~/.bashrc` 
```bash
source ~/.bashrc
```

# 常用基础指令

对于一个目录，打开gitbash窗口，可以输入：
```bash
git init    # 初始化一个仓库

git add "文件名"      # 工作区 -> 暂存区
# 经常会用 `git add .` 来直接提交所有的文件

git commit  # 暂存区 -> 本地仓库
# 可以用 `git commit -m "这次提交的标题"` 对此次提交内容进行总结

git status  # 查看文件的修改状态

git log     # 查看提交日志
```

`git log` 还可以选用以下参数：
- `--all` ：显示所有分支
- `--pretty=oneline` ：将提交信息显示为一行
- `--abbrev-commit` ：使得输出的 commit 更加简短
- `--graph` ：以图的形式显示

如果希望在执行 `git add .` 的时候不要添加某些文件，可以创建文件 `.gitignore` ，再进行操作：
```bash
touch .gitignore
vi .gitignore
```

再 `vi` 中进行操作时，可以用 `*.a` 来实现忽略 `.a` 类型的文件，其余文件类型类似