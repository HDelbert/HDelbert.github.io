---
title: "awk 使用简介"
date: 2016-06-15 20:45:40 +08:00
tags: awk
---

Awk是一个使用很方便的编程语言，特别在文本处理方面。这是一篇简短的使用说明书，由于笔者不是经常使用Awk，而每次要用的时候都会忘掉一些语法和技巧，特此记一篇使用说明。本文大部分内容摘抄自[《AWK程序设计语言》](https://github.com/wuzhouhui/awk)第一章。

## 开始

首先假设我们有一个数据文件emp.data，这个文件包含有名字，每小时工资，工作时长，每一行代表一个雇员的纪录，就像这样

| Beth | 4.00 | 0 |
| Dan | 3.75 | 0 |
| Kathy | 4.00 | 10 |
| Mark | 5.00 | 20 |
| Mary | 5.50 | 22 |
| Susie | 4.25 | 18 |

一些变量的含义：

* $0表示整行数据
* $1，$2，$3表示第一列，第二列，第三列的数据，依次类推。如第一行数据$1==Beth，$2==4.00
* NF，字段的数量。如$NF为最后一个字段
* NR，到目前为止，读取到的行数。

命令行输入的格式：

```
$ awk 'command' data
```

被运行的程序用**单引号**包围起来，data为输入的数据文件。被单引号包围的部分是一个完整的awk程序。它由一个单独的**模式－动作**语句组成，即*`pattern { action }`*。如`awk '$3 > 0 { print $1, $2 * $3 }' emp.data`。`$3 > 0`是模式，动作为`{ print $1, $2 * $3 }`。

## 运行AWK程序

运行一个awk程序有多种方式

* `$ awk 'program' input files`
* `$ awk 'program'`接下来一行一行的输入你的数据
* `$ awk -f progfile optional list of files`-f选项告诉awk从文件中提取程序。

## 输出 print, printf

print可以直接输出数据，字符串等。如 { print NR, $1, $2 * $3, }
printf可以做格式化输出，printf(*format*, *value1*, *value2*,...,*valuen*)，format中的格式和c语言类似。

## BEGIN 与 END

BEGIN在文件的第一行处理之前被匹配（执行），END在文件的最后一行处理之后匹配。

## 流程控制语句

支持 If-Else语句，while语句和for语句。它们只能用在action中。格式如下：

If-Else语句
``` shell
$2 > 6 { n = n + 1; pay = pay + $2 * $3 }
END { if (n > 0)
        print n, "employees, total pay is", pay,
                 "average pay is", pay/n
      else
        print "no employees are paid more than $6/hour"
    }
```

While语句
``` shell
{   i = 1
    while (i <= $3) {
        printf("\t%.2f\n", $1 * (1 + $2) ^ i)
        i = i + 1
    }
}
```

For语句
``` shell
{   for (i = 1; i <= $3; i = i + 1)
        printf("\t%.2f\n", $1 * (1 + $2) ^ i)
}
```

## 数组
``` shell
{ line[NR] = $0 }
END { i = NR
      while (i > 0) {
          print line[i]
          i = i - 1
      }
    }
```

## 实用“一行”手册

1. 输入行的总数

       END { print NR }

2. 打印第10行

       NR == 10

3. 打印每一个输入的最后一个字段

       { print $NF }

4. 打印最后一行的最后一个字段

        { field = $NF }
        END { print field }

5. 打印字段数多于4个的输入行

        NF > 4

6. 打印最后一个字段值大于4的输入行

        $NF > 4

7. 打印所有输入行的字段数的总和

        { nf = nf + NF }
        END { print nf }

8. 打印包含Beth 的行的数量

        /Beth/ { nlines = nlines + 1 }
        END { print nlines }

9. 打印具有最大值的第一个字段, 以及包含它的行(假设$1 总是正的)

        $1 > max { max = $1; maxline = $0 }
        END { print max, maxline }

10. 打印至少包含一个字段的行

        NF > 0

11. 打印长度超过80 个字符的行

        length($0) > 80
12. 在每一行的前面加上它的字段数

        { print NF, $0 }

13. 打印每一行的第1 与第2 个字段, 但顺序相反

        { print $2, $1 }

14. 交换每一行的第1 与第2 个字段, 并打印该行

        { temp = $1; $1 = $2; $2 = temp; print }

15. 将每一行的第一个字段用行号代替

        { $1 = NR; print }

16. 打印删除了第2 个字段后的行

        { $2 = ""; print }

17. 将每一行的字段按逆序打印

        { for (i = NF; i > 0; i = i - 1) printf("%s ", $i)  
            printf("\n")  
        }

18. 打印每一行的所有字段值之和

        { sum = 0
            for (i = 1; i <= NF; i = i + 1) sum = sum + $i
            print sum
        }

19. 将所有行的所有字段值累加起来

        { for (i = 1; i <= NF; i = i + 1) sum = sum + $i }
        END { print sum }

20. 将每一行的每一个字段用它的绝对值替换

        { for (i = 1; i <= NF; i = i + 1) if ($i < 0) $i = -$i
            print $i
        }
