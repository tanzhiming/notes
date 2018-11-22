[TOC]


# 学习GNU EMACS

## 文件编辑

### 光标移动

```
C-f     光标向右移动一个字符
C-b     光标向左移动一个字符
C-p     光标向上移动一行
C-n     光标向下移动一行 
M-f     光标向右移动一个单词
M-b     光标向左移动一个单词
C-a     光标移动到一行的开始
C-e     光标移动到一行的结束
M-a     光标向左移动一个句子
M-e     光标向右移动一个句子
M-}     光标向下移动一个段落
M-{     光标向上移动一个段落
C-x ]   光标移动到下一页
C-x [   光标移动到上一页
C-v     显示下一屏
M-v     显示前一屏
M->     移动到编辑缓存区的末尾
M-<     移动到编辑缓存区的开始
M-x goto-line n <RETURN>   光标移动到文件的第n行
M-x goto-char n <RETURN>   光标移动到文件的第n个字符
```                           
> C-q C-l 插入分页符，"C-q" 叫做 “引用” 命令, 告诉Emacs要把"C-l"字符插入到文件里去。


### 命令的重复执行

```
1. 重复执行的命令前加上"ESC n"，如："ESC 500 C-n" 表示光标向下移动500行
2. 重复执行的命令前加上"C-u"
```

### 重新绘制屏显画面

```
C-l     重新绘制屏显页面，当前行放到画面中心处
```


### 光标移动命令速查表
|键盘操作|命令名称|动作|
|---|---|---|
|C-f|forward-char|光标前移一个字符（右）|
|C-b|backward-char|光标后移一个字符（左）|
|C-p|previous-line|光标前移一行（上）|
|C-n|next-line|光标后移一行(下)|
|M-f(ESC f)|forward-word|光标前移一个单词|
|M-b(ESC b)|backward-word|光标后移一个单词|
|C-a|beginning-of-line|光标移动行首|
|C-e|end-of-line|光标移动行尾|
|M-e(ESC e)|forward-sentence|光标前移一个句子|
|M-a(ESC a)|backward-sendtence|光标后移一个句子|
|M-}(ESC })|forward-paragraph|光标前移一个段落|
|M-{(ESC {)|backward-paragraph|光标后移一个段落|
|C-v|scroll-up|屏幕上卷一屏|
|M-v(ESC v)|scroll-down|屏幕下卷一屏|
|C-x ]|forward-page|光标前移一页|
|C-x [|backward-page|光标后移一页|
|M-<(ESC <)|beginning-of-buffer|光标移动到文件头|
|M->(ESC >)|end-of-buffer|光标移动到文件尾|
|无|goto-line|光标前进到文件的第n行|
|无|goto-char|光标前进到文件的第n个字符|
|C-l|recenter|重新绘制屏显画面，当前行放在画面中心处|
|ESC n|digit-argument|重复执行n次后续命令|
|C-u n|universal-argument|重复执行n次后续命令(省略n时重复4次)|




### 文本删除命令速查表

|键盘操作|命令名称|动作|
|---|---|---|
|C-d|delete-char|删除光标位置上的字符|
|DEL|delete-backword-char|删除光标前面的字符|
|M-d|kill-word|删除光标后面的单词|
|M-DEL|backward-kill-word|删除光标前面的单词|
|C-k|kill-line|从光标位置删除到行尾|
|M-k|kill-sentence|删除光标后面的句子|
|C-x DEL|backward-kill-sentence|删除光标前面的句子|
|C-y或者SHIFT-INSERT|yank|恢复被删除的文本|
|C-w或者SHIFT-DELETE|kill-region|删除文本块|
|无|kill-paragraph|删除光标后面的段落|
|无|backward-kill-paragraph|删除光标前面的段落|


### 文本块操作命令速查表

|键盘操作|命令名称|动作|
|---|---|---|
|C-@ 或 C-SPACE|set-mark-command|标记文本块的开始（或结束）位置|
|C-x C-x|exchange-point-end-mark|互换插入点和文本标记的位置|
|C-w 或 SHIFT-DELETE|kill-region|删除文本块|
|C-y 或 SHIFT-INSERT|yank|粘贴最近删除或复制的文本|
|M-w 或 C-INSERT|kill-ring-save|复制文本块|
|M-h|mark-paragraph|标记段落|
|C-x C-p|mark-page|标记页面|
|C-x h|mark-whole-buffer|标记整个缓存区|
|M-y|yank-pop|在用过“C-y”命令以后粘贴更早删除的文本|

### 段落重排命令速查表

|键盘操作|命令名称|动作|
|---|---|---|
|M-q|fill-paragraph|重排段落|
|无|fill-region|对某个文本块中的段落进行重排|


### 位置交换命令速查表

|键盘操作|命令名称|动作|
|---|---|---|
|C-t|transpose-chars|交换两个字符的位置|
|M-t|traspose-words|交换两个单词的位置|
|C-x C-t|transpose-lines|交换连个文本行的位置|
|无|transpose-sentences|交换两个句子的位置|
|无|transpose-paragraphs|交换两个段落的位置|


### 字母大小写编辑命令速查表

|键盘操作|命令名称|动作|
|---|---|---|
|M-c|capitalize-word|把单词的首字母改为大写|
|M-u|upcase-word|把单词的字母全部改为大写|
|M-l|downcase-word|把单词的字母全部改为小写|
|M-- M-c(ESC - ESC c)|negtive-arguments;capitalize-word|把前一个单词的首字母改为大写|
|M-- M-u(ESC - ESC u)|negtive-arguments;upcase-word|把前一个单词的字母全部改为大写|
|M-- M-l(ESC - ESC l)|negtive-arguments;downcase-word|把前一个单词的字母全部改为小写|



### 命令中止和撤销
|键盘操作|命令名称|动作|
|---|---|---|
|C-g|keyboard-quit|放弃当前命令|
|C-x u|advertised-undo|撤销上一次编辑（可以重复使用）|
|C-_或C-/|undo|撤销上一次编辑|
|无|revert-buffer|把缓冲区恢复到上次对文件进行存盘（或者自动存盘）时的状态|

### 递增查找命令速查表
|键盘操作|命令名称|动作|
|---|---|---|
|C-s|isearch-forward|向前（朝文件尾方向）开始递增查找操作；后面是查找字符串。另外、（向前）查找下一个出现查找字符串的地方|
|C-r|isearch-backword|向后（朝文件头方向）开始递增查找操作；后面是查找字符串。另外、（向后）查找下一个出现查找字符串的地方|
|RETURN|（无）|退出查找操作|
|C-g|keyboard-quit|取消递增查找操作（你可能需要连接按它两次）|
|DEL|(无)|删除查找字符串中的字符|
|C-s C-w|(无)|开始递增查找；把光标位置处的单词用做查找字符串|
|C-s C-y|（无）|开始递增查找；把光标位置到行尾之间的文本用做查找字符串|
|C-s M-y|（无）|开始递增查找；把删除环的文本用做查找字符串|
|C-s C-s|（无）|重复刚才的查找操作|

### 简单查找命令速查表

|键盘操作|动作|
|---|---|
|C-s RETURN searchstring RETURN|向前（朝文件尾方向）开始一次非递增查找操作|
|C-s|向前查找下一个|
|C-r RETURN searchstring RETURN|向后（朝文件头方向）|
|C-r|向后查找下一个|


### 查找替换




