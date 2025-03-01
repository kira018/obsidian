1. 聚合模式
聚合模式由各个模块提供自己的feign接口和biz接口，需要的时候就通过pom引入
1. 独立模块
抽取一个公共的fefin-common模块，放所有模块的feigin接口。