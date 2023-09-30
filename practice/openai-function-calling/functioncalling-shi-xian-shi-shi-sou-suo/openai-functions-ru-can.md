# 设定functions 入参

在调用 Openai 接口时，我们只需要添加 functions 入参，即可用让其判断，prompt 中是否有话术需要调用开发方提供的方法。

{% embed url="https://gist.github.com/tripluyi/edce4bc0b12f3a135a4d0964e2018d48" %}

可以看到，这里实际比之前的 [stream 调用](https://gist.github.com/tripluyi/0dcfb7e001ba80f97a60fb22348209bd)只多了一个 functions 入参。

但这仅仅是第一步，由于有需要调用第三方 function 的存在，实际我们开发的时候，还需要另外处理每次返回的内容。
