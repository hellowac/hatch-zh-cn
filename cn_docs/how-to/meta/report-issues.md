# 如何报告问题

-----

所有关于异常行为的报告都应通过 [`self report`](../../cli/reference.md#hatch-self-report) 命令生成：

```
$ hatch self report
```

默认情况下，此命令会在默认浏览器中打开一个新标签页，预填充关于您环境的信息。

如果 Hatch 未与图形浏览器一起安装，您也可以传入 `--no-open`/`-n` 参数，以输出带有正确参数的 URL，便于复制到其他地方：

```
$ hatch self report -n
https://github.com/pypa/hatch/issues/new?body=%23%23+Current+behavior%0A%3C%21--+A+clear+and+concise+description+of+the+behavior.+--%3E%0A%0A%23%23+Expected+behavior%0A%3C%21--+A+clear+and+concise+description+of+what+you+expected+to+happen.+--%3E%0A%0A%23%23+Additional+context%0A%3C%21--+Add+any+other+context+about+the+problem+here.+If+applicable%2C+add+screenshots+to+help+explain.+--%3E%0A%0A%23%23+Debug%0A%0A%23%23%23+Installation%0A%0A-+Source%3A+pip%0A-+Version%3A+1.9.2.dev5%0A-+Platform%3A+Windows%0A-+Python+version%3A%0A++++%60%60%60%0A++++3.11.1+%28tags%2Fv3.11.1%3Aa7a450f%2C+Dec++6+2022%2C+19%3A58%3A39%29+%5BMSC+v.1934+64+bit+%28AMD64%29%5D%0A++++%60%60%60%0A%0A%23%23%23+Configuration%0A%0A%60%60%60toml%0Amode+%3D+%22local%22%0Ashell+%3D+%22nu%22%0A%60%60%60%0A
```
