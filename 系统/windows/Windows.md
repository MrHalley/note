# Windows

[TOC]



## 常规问题解决方案

[Windows下如何查看某个端口被谁占用 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/windows-finds-port-usage.html)



## Bat脚本

```bash
# 以下是常用命令及其解析，也可通过 command /? 到window bash 中查看更加详细的解析
```

###  1、@echo off 关闭了命令回显



###  2、rem 注释

```bash
@echo off

echo This is a line of code & :: This is a comment
echo This is another line
```

 在这个示例中，第一行使用 @echo off 关闭了命令回显。接下来，使用 :: 来添加注释，然后在注释后面添加 &，以避免将注释视为代码。然后，在同一行上，添加了一个实际的命令 echo This is a line of code。这会在执行时只输出命令行中的注释，而不会执行实际的代码部分。
 请注意，这只是一个技巧，可能会使代码变得难以理解。通常情况下，建议使用 rem 关键字添加正式的注释，并保持代码的可读性。



###  3、&

在 Windows 命令提示符和批处理脚本中，`&` 是一个用于连接多个命令的命令分隔符。它允许在一行上执行多个命令，而不必换行。

当你在命令提示符或批处理脚本中使用 `&` 时，它会将前一个命令和后一个命令连接在一起，使它们在同一行上执行。这在一些简单的情况下很有用，可以将多个命令组合成一个命令行。然而，过多使用 `&` 可能会使代码变得难以阅读和维护。

以下是一个示例：

```bash
echo Hello & echo World
```

在这个示例中，两个 `echo` 命令通过 `&` 连接在一起，输出结果将会是：

```bash
Hello
World
```

需要注意的是，虽然 `&` 可以连接多个命令，但它只是一个简单的命令分隔符，不具备控制流程的能力。如果你需要更复杂的逻辑，可能需要使用其他命令分隔符或者在脚本中使用条件、循环等结构



###  4、`::`  作为注释标记：

在批处理脚本中，`::` 用于添加注释。与 `rem` 关键字一样，`::` 后面的内容被视为注释，不会被执行。例如：

```bash
:: This is a comment
echo This is a command
```

在这个示例中，`:: This is a comment` 是一个注释，`echo This is a command` 是一个实际的命令。

需要注意的是，`::` 注释只能在新行的开头使用，而不能在命令行中的其他位置。与 `rem` 关键字不同，`::` 注释在一些特殊情况下可能会导致问题，因此在注释的同时，仍建议使用 `rem` 关键字来保持代码的稳定性。



###  5、setlocal enabledelayedexpansion 启用延迟变量扩展

`setlocal enabledelayedexpansion` 是一个 Windows 批处理脚本命令，用于启用延迟变量扩展。它允许你在脚本中使用 `!` 来引用变量，并确保变量在运行时而不是解析时进行扩展。这在需要在循环或代码块中引用动态变量值时特别有用。

下面是 `setlocal enabledelayedexpansion` 的用法示例：

```bash
@echo off

rem 启用延迟变量扩展
setlocal enabledelayedexpansion

rem 设置一个变量
set my_variable=Hello, World!

rem 在代码块中使用 ! 引用变量
echo Variable with delayed expansion: !my_variable!

rem 进入循环
for %%a in (1,2,3) do (
    set inner_variable=Loop iteration %%a
    rem 在循环中使用 ! 引用变量
    echo !inner_variable!
)

rem 关闭延迟变量扩展
endlocal
```

在这个示例中，`setlocal enabledelayedexpansion` 启用了延迟变量扩展。在代码块和循环中，我们使用 `!` 来引用变量，确保变量值在运行时扩展。

需要注意的是，`!` 在启用延迟变量扩展时才会被解释为变量引用，而在普通的变量扩展中仍然被视为普通字符。因此，只有在需要延迟扩展的情况下使用 `!`。

在完成使用延迟变量扩展后，你可以使用 `endlocal` 命令来关闭延迟扩展，将环境还原为正常的变量扩展方式。



`endlocal`： Windows 批处理脚本在退出时会自动执行 `endlocal` 命令来关闭通过 `setlocal` 命令创建的局部环境。这确保了在脚本结束时，局部环境中的变量和资源得到正确的清理和释放。

### 6、set 设置变量


####   6.1、set /p 等待用户输入

 `set /p` 是 Windows 批处理脚本中用来读取用户输入的命令。它允许脚本暂停执行，等待用户输入一段文本，并将用户输入的文本保存到一个指定的变量中。这使得批处理脚本能够与用户进行交互，根据用户的输入进行不同的操作。
#下面是 set /p 命令的基本格式：
set /p variable=prompt_message

- variable：要保存用户输入的变量的名称。
- prompt_message：在等待用户输入时显示给用户的提示消息。


####  6.2、set /a 算术表达式

 ```bash
@echo off

rem 执行多个算术运算
set /a sum=5+3
set /a difference=10-4
set /a product=6*2
set /a quotient=12/3

echo Sum: %sum%
echo Difference: %difference%
echo Product: %product%
echo Quotient: %quotient%

 ```



###  7、for 循环

格式

```bash
for %%variable in (set) do command

另外，FOR 变量参照的替换已被增强。你现在可以使用下列
选项语法:
%~I          - 删除任何引号(")，扩展 %I
%~fI        - 将 %I 扩展到一个完全合格的路径名
%~dI        - 仅将 %I 扩展到一个驱动器号
%~pI        - 仅将 %I 扩展到一个路径
%~nI        - 仅将 %I 扩展到一个文件名
%~xI        - 仅将 %I 扩展到一个文件扩展名
%~sI        - 扩展的路径只含有短名
%~aI        - 将 %I 扩展到文件的文件属性
%~tI        - 将 %I 扩展到文件的日期/时间
%~zI        - 将 %I 扩展到文件的大小
%~$PATH:I   - 查找列在路径环境变量的目录，并将 %I 扩展
              到找到的第一个完全合格的名称。如果环境变量名
              未被定义，或者没有找到文件，此组合键会扩展到
              空字符串
```




###  8、for /F  循环用于从文件中逐行读取数据

格式：

```bash
for /F "options" %%variable in (filename) do command
```

- `options`：附加选项，用于指定如何解析循环的每一行。

  - `tokens`：指定要从每行提取的令牌（字段）。
  - `delims`：指定用于分隔令牌的分隔符字符。
  - `skip`：指定要跳过的行数。
  - `eol`：指定注释字符，跳过以此字符开头的行。
  - `usebackq`：用于处理带空格的文件名。
- `%%variable`：循环变量，每次循环会被赋值为每一行的内容。
- `filename`：要从中读取数据的文件名。
- `command`：在每次循环中要执行的命令。

示例：

```bash
@echo off

rem 逐行读取 data.txt 文件中的内容
for /F "tokens=1,2 delims=," %%a in (data.txt) do (
  echo First token: %%a
  echo Second token: %%b
)

FOR /F "eol=; tokens=2,3* delims=, " %i in (myfile.txt) do @echo %i %j %k

# 会分析 myfile.txt 中的每一行，忽略以分号打头的那些行，将每行中的第二个和第三个符号传递给 for 函数体，用逗号和/或空格分隔符号。请注意，此 for 函数体的语句引用 %i 来获得第二个符号，引用 %j 来获得第三个符号，引用 %k来获得第三个符号后的所有剩余符号。对于带有空格的文件名，你需要用双引号将文件名括起来。为了用这种方式来使用双引号，还需要使用 usebackq 选项，否则，双引号会被理解成是用作定义某个要分析的字符串的。
```

  

###  9、for /L 循环用于遍历一定范围内的整数



```bash
for /L %%variable in (start, step, end) do command
```

- `%%variable`：循环变量，每次循环会取值从 `start` 开始递增 `step` 直到 `end`。
- `start`：循环开始的整数值。
- `step`：循环的步长，即每次递增的值。
- `end`：循环结束的整数值。
- `command`：在每次循环中要执行的命令。

示例：

```bash
for /L %%i in (1,1,5) do (
    echo Loop iteration %%i
)
```



### 10、变量解析

- %1、%2  ...  %n：获取执行脚本或者脚本中代码块的第n个参数
- %variable%:  获取set variable=xx  的变量
- !variable!:  启用延迟变量拓展时获取变量的方式
- %%variable:  for循环中用的变量格式



### 11、转义 `^` `%%`

在 Windows 批处理脚本中，转义字符通常是 `^`（插入符号）。它用于告诉脚本解释器不要解释后续字符，而是将其视为普通字符。这在处理特殊字符或空格时非常有用。

```bash
@echo off
setlocal enabledelayedexpansion
rem 修改的节点数
set end=4

rem 执行 git log 命令并将输出传递给脚本
for /f "tokens=*" %%a in ('git log -n 4 --pretty^="%%H"') do (
    set commit_info=%%a
    echo Commit Info: !commit_info!
    rem 在这里可以对 commit_info 进行进一步处理
)
endlocal    
echo 程序已执行完毕！按下任意键退出...
pause
```

### 场景脚步

#### 修改最近历史节点提交时间

以下是依次修改Git提交时间的bat脚步请参考：

```bash
@echo off
rem 修改git历史最近提交节点时间的脚本

rem 修改的节点数
set reset_count=%1

rem 依次输入修改提交的时间
git rebase -i HEAD~%reset_count%
for /L %%i in (1,1,%1) do (
    setlocal enabledelayedexpansion
    set /p "input=开始重置最近历史节点 %%i （反） : "
    REM 退出命令
    if "!input!"=="exit" (
        REM 终止rebase
        git rebase --abort
        echo Exiting ...
        REM 退出后会自动执行endlocal来清理局部变量
        exit /b
    )
    git commit --amend --date="!input!"
    git rebase --continue
    endlocal    
)
echo 程序已执行完毕！按下任意键退出...
pause
REM 注意：运行此脚本默认所有节点都是edit模式，如果需要动态获取edit的数量，需要读取./git/rebase-merge/git-rebase-todo文件，目前没有此需求就没有写那么复杂了





```
git-rebase-todo文件示例：
```
edit 9383b4b 获取招标会列表、查看-招标信息
edit 1494dc5 报名编辑回显
edit c7762bd 议价管理列表

# Rebase 48f58fe..c7762bd onto 48f58fe (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```





#### 遍历文件

```bash
@echo off

rem 调用递归遍历子例程
call :ProcessFolder "%parent_folder%"

echo 程序已执行完毕！按下任意键退出...
pause
goto :eof

:ProcessFolder
rem 遍历当前文件夹中的文件
for %%f in ("%~1\*.*") do (
    echo File found: %%~nxf
)

rem 遍历当前文件夹中的子文件夹
for /d %%d in ("%~1\*") do (
    echo Folder found: %%d
    call :ProcessFolder "%%~d"
)

goto :eof
```



#### 批量向项目中.gitignore文件添加添加或删除文件的脚本

> addContent.bat

```bash
@echo off

setlocal enabledelayedexpansion
rem 设置要追加的内容
set content=*.iml
rem 设置要遍历的父文件夹路径
set parent_folder=D:\Java\Project\work\erong

rem 使用 for /d 遍历文件夹
for /d %%d in ("%parent_folder%\ldw-*") do (
    for %%f in ("%%d\*.gitignore") do (
        echo %%d
        echo File: %%f
        REM 添加文本出错时，调用删除脚本
        REM call deleteContent.bat %%d !content!
        echo.>>"%%f"
        echo %content%>>"%%f"
    )
)

endlocal

echo 程序已执行完毕！按下任意键退出...
pause
```



> deleteContent.bat

```bash
@echo off
rem %1 要修改文件的目录  %2 要删除的内容（删除包含内容的行）

setlocal enabledelayedexpansion

rem 设置要处理的文件路径
set file_path=%~1\.gitignore
echo file_path: %file_path%

rem 设置要删除的内容
set content_to_delete=%~2

rem 临时文件
set tempfile=%1\tempfile.txt
echo tempfile: %tempfile%

rem 创建临时文件
type nul > %tempfile%

rem 读取文件内容并删除指定内容
(for /f "usebackq delims=" %%a in ("%file_path%") do (
    set "line=%%a"
    set "modified_line=!line!"
    echo !modified_line!|findstr /v /c:"!content_to_delete!" >nul
    if !errorlevel!==0 echo !modified_line!
)) >%tempfile%

rem 将修改后的内容写回到原文件
move /y %tempfile% "%file_path%"
echo File content modified.
REM pause

```



#### cherry-pick重新提交脚本

> 批量cherry-pick

```
@echo off
setlocal enabledelayedexpansion
rem 设置编码utf-8以便正确解码git log的中文提交信息
chcp 65001

rem cherry-pick的分支
set target_branch=temp

rem 批量cherry-pick 

rem 修改的节点数 
set /p "start=请输入开始的节点数"
rem cherry-pick节点的个数
set count=1
set current=1

rem 在 Windows 命令行中设置字符编码为 UTF-8
rem 依次从另一分支的第start条记录开始cherry-pick到当前分支,并重新提交
for /f "tokens=1,2" %%a in ('git log -n !start! --reverse --pretty^="%%H %%s" %target_branch%') do (
    echo %%a %%b
    if !current! leq !count! (
        echo cherry-pick此节点并重新提交到当前分支
        git cherry-pick -n %%a
        git commit -m %%b
    )
    set /a current += 1
)
endlocal    
echo 程序已执行完毕！按下任意键退出...
pause

```

> 单次cherry-pick

```bash
@echo off
setlocal enabledelayedexpansion
chcp 65001


set /p "module=模块名："
set /p "branch_name=分支名："
set /p "reset_count=修改节点数："
echo 当前模块 !module!
echo 目标分支 !branch_name!

for /L %%i in (1,1,!reset_count!) do (
    
    set /p "commit_hash=cherry-pick的提交hash编码(输入exit退出,输入module查看模块名):"
    if "!commit_hash!"=="module" (
        echo !module!
        set /p "commit_hash=cherry-pick的提交hash编码(输入exit退出):"
    )
   
    if "!commit_hash!"=="exit" (
        REM 终止rebase
        echo Exiting ...
        REM 退出后会自动执行endlocal来清理局部变量
        exit /b
    )
    
    rem 单次cherry-pick
    for /f "tokens=1,2" %%a in ('git log -n 1 !commit_hash! --pretty^="%%H %%s"') do (
        echo %%a %%b
        echo cherry-pick此节点并重新提交到指定分支
        
        rem 获取当前分支名称
        for /f "tokens=*" %%a in ('git branch --show-current') do (
            set current_branch_name=%%a
        )
        rem 当前分支不是目标分支
        if !current_branch_name! == !branch_name! (
            git cherry-pick -n %%a
            echo commit
            git commit -m %%b
        ) else (
            echo 当前所在分支!current_branch_name!,切换分支!branch_name!
            git checkout !branch_name!
            git cherry-pick -n %%a
            echo commit
            git commit -m %%b
            git checkout !current_branch_name!
        )
    )
    
)
echo 程序已执行完毕！按下任意键退出...
pause
```

