![npm (scoped)](https://img.shields.io/badge/version-2.1.6-blue.svg)
# import-three-examples
this is webpack plugin, It was created to solve the problem of threeJs modular development.  
不会额外增大打包之后得文件体积!原项目webpack结构无需改动!  
no big!no change!  

`npm i imports-loader exports-loader --save-dev`
<br/>
`npm i import-three-examples --save-dev`

国内用户如果npm下载卡住,请更换成cnpm下载

## webpack config
```
const ThreeExamples = require('import-three-examples')

module.exports = {
  ......
  ......
  module: {
    rules: [
      ......
      ......
      {
        test: /\.js$/,
        loader: 'babel-loader'
      },
      ...ThreeExamples
    ]
  }
}
```

## render page/js
```
import OrbitControls from "three/examples/js/controls/OrbitControls"
import FBXLoader from "three/examples/js/loaders/FBXLoader"

......
......

let controls = new OrbitControls(camera, el)

let fbx = new FBXLoader()

fbx.load(url, function (_obj) {
  console.log(_obj)
})

......
......
```

## 2019/01/11 vue-cli 3.0+ 的webpack配置:
vue.config.js
```
const ThreeExamples = require('import-three-examples')

module.exports = {
    chainWebpack: config => {
        ThreeExamples.forEach((v) => {
            if (~v.use.indexOf('imports')) {
                config.module
                    .rule(`${v.test}_i`)
                    .test(require.resolve(v.test))
                    .use(v.use)
                    .loader(v.use)
            } else {
                config.module
                    .rule(`${v.test}_e`)
                    .test(require.resolve(v.test))
                    .use(v.use)
                    .loader(v.use)
            }
        })
    }
}
```

## 2019/01/17  nuxt 中的配置
nuxt.config.js
```
const ThreeExamples = require('import-three-examples')

module.exports = {
  build: {
    extend(config, ctx) {
      ThreeExamples.forEach((v, i) => {
        config.module.rules.push({
          test: require.resolve(v.test.split('node_modules\\')[1]),
          use: v.use
        })
      })
    }
  }
}
```

## 2019/03/08  说明一下(仅针对webpack不太熟的童靴)
最近很多人反馈这个插件怎么不起作用啊  
结果都是因为引入了本地模型，但是未对模型设置webpack加载器  
下面用fbx和obj模型作为例子,教大家怎么对模型设置webpack加载器。其他格式的模型/\.(fbx|obj)$/中的fbx和obj替换成你们需要的模型，多种格式间用 | 衔接  
首先一定要cnpm i url-loader --save-dev (如果模型太大可以使用flie-loader)!!!!!!!!!!  
### webpack
同样是在module.rules 中添加
```
  {
    test: /\.(fbx|obj)$/,
    loader: 'url-loader'
  },
  (下面就是最上面对应的引入插件的方法)
```
### vue-cli 3.0
vue.config.js
```
const ThreeExamples = require('import-three-examples')

module.exports = {
    chainWebpack: config => {
        config.module
            .rule('obj')
            .test(/\.(obj|fbx)$/)
            .use('file-loader')
            .loader('file-loader')
            (下面就是最上面对应的引入插件的方法)
    }
}
```
