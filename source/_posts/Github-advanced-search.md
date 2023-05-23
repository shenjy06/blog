---
title: Github advanced search
date: 2023-05-22 21:30:54
tags:
- Github
- Search
---

## [Github Advanced Search][1]

> 学好Github搜索，可以在Github中找到自己需要的项目，一定要熟练使用Github的站内**搜索**。
>
> 学习文档如下:
>
> - [官网文档][2] 
> - [官方文档-zh][3]
> - [关于在GitHub上搜索][4]



下图于2023年5月22日截取于Github网站。

![github_advanced_search](https://user-images.githubusercontent.com/40328786/239930540-11365707-cc8c-4705-9b7f-a583f781e151.png)

### 高级选项

| 具体用法                        | 例子                              |
| ------------------------------- | --------------------------------- |
| **user:**MarkShen1992           | `github, atom, electron, octokit` |
| **repo:**MarkShen1992/leet-code | `twbs/bootstrap, rails/rails`     |
| **created:**>2023-01-01         | `>YYYY-MM-DD, YYYY-MM-DD`         |
| **language:**Java               | [programming lang][6]             |

### 仓库选项

| 具体用法                                                | 例子                    |
| ------------------------------------------------------- | ----------------------- |
| **stars:**>1000                                         | `0..100, 200, >1000`    |
| **forks:**>1000                                         | `50..100, 200, <5`      |
| **size:**100KB                                          | `Repository size in KB` |
| **pushed:**<2020-01-01                                  | `<YYYY-MM-DD`           |
| **license:**artistic-2.0                                | `license:artistic-2.0`  |
| Return repositories **not\|and\|only** including forks. | -                       |

### Code 选项

| 具体用法                                        | 例子                 |
| ----------------------------------------------- | -------------------- |
| **path:***.rb                                   | `rb, py, jpg`        |
| **path:**/net                                   | `/foo/bar/baz/qux`   |
| **path:****/README.md                           | `app.rb, footer.erb` |
| Return code **not\|and\|only** including forks. | -                    |

### Issues 选项

| 具体用法                | 例子                                       |
| ----------------------- | ------------------------------------------ |
| **state:**open\|close   | `open|close`                               |
| **reason:**completed    | `completed|not planned|reopened`           |
| **comments:**100..1000  | `100..1000, 100, >100, >=100, <100, <=100` |
| **label:**bug           | `bug, ie6`                                 |
| **author:**octocat      | `hubot, octocat`                           |
| **mentions:**mattt      | `tpope, mattt`                             |
| **assignee:**jim        | `twp, jim`                                 |
| **updated:**<2020-01-01 | `<2020-01-01`                              |

### 用户选项

| 具体用法                  | 例子                  |
| ------------------------- | --------------------- |
| **fullname:**MarkShen1992 | `Grace Hopper`        |
| **location:**San          | `San Francisco, CA`   |
| **followers:**20..50      | `20..50, >100`        |
| **repos:**<100            | `<100, 100..200`      |
| **language:**C++          | [programming lang][6] |

### Wiki选项

| 具体用法            | 例子          |
| ------------------- | ------------- |
| updated:<2023-01-01 | `<YYYY-MM-DD` |

### 平时经常使用的一些例子

- 2020-01-01之前的仓库

```
created:<2020-01-01
```

- git 学习资料

```
# 从repo名称和描述中匹配
git 最好 学习 资料
```

- 基于上面的搜索结果, 我想搜索在 `readme` 文件中包含上面的词汇的项目, 或者在具体的某个文件。做推广, 每个工程的`readme`要好好设计里面的内容。

```
git 最好 学习 资料 in:readme
```

- 如何在原来的条件上再过滤，根据 **stars**，**forks**来搜索

```
git 最好 学习 资料 in:readme stars:>1000 forks:>1000 language:Java
```

- 搜索自己想要的代码, 一定要**登录**

```
'after_script:' + 'stage: deploy' filename:.gitlab-ci.yml
```

- 博客

```
blog easily start in:readme stars:>5000
```



### [还有一些例子][5]

完~

[1]:https://github.com/search/advanced
[2]:https://docs.github.com/en/search-github
[3]: https://docs.github.com/zh/search-github
[4]:https://docs.github.com/zh/search-github/getting-started-with-searching-on-github/about-searching-on-github
[5]: https://gist.github.com/bonniss/4f0de4f599708c5268134225dda003e0
[6]:https://github.com/collections/programming-languages
