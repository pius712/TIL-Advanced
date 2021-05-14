# Webpack
module bundler

dependency graph 를 만들어서 각각의 모듈을 맵핑 시키고 이를 번들로 만들어 준다.


- Entry
- Output
- Loader
- Plugins
- Mode


## Entry
어떤 모듈을  bundling 할것인지에 대한 결정을 하는 것으로 이에 포함되는 파일을 가지고  dependency graph를 그리고 결과를 만들어낸다.

```javascript
entry : path.join(__dirname, "src", "index.js"),
```

## Output

만들어진  bundle을 어디에, 어떤 이름으로 emit 할 것인가에 대한 설정을 할 수 있다.


```javascript
output: {
	path: path.join(__dirname, "build"), 
	filename: "index.bundle.js"
},

```


## Loader

Loader는 webpack이  다른 타입의 파일들을 처리할 수 있도록 해줌
예를들어서, style, image 등등을 자바스크립트 파일에서  import 할 수 있는 이유는 webpack에서 로더를 설정해줬기 때문에 가능한 것

```javascript
module : {
    test: /\.(js|jsx)$/,
    exclude: /node_modeuls/,
    use: ["babel-loader"]
},
```

## Plugins
bundle optimization, asset management, 환경변수 삽입과 같은 넓은 범위의 작업을 수행할 수 있다.

```javascript
plugins: [
    new HtmlWebpackPlugin({template: path.join(__dirname, "public", "index.html")})
]

```

html-webpack-plugin은   template 옵션 안에 명시된  html 파일에, 웹팩 빌드를 통해 설정된  bundle 을 생성해서 주입해주는 역할을 해준다.


## Mode
By setting the mode parameter to either development, production or none, you can enable webpack's built-in optimizations that correspond to each environment. The default value is production.


```javascript
mode: process.env.NODE_ENV || "development",
```



#Toolchain/Webpack

