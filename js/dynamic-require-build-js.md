``` javascript
var fs = require('fs');

var commconConfigContent = fs.readFileSync("../global/commonconfig.js").toString();
var configParams = commconConfigContent.split("// configend")[0].split("// configstart")[1];

var Module = module.constructor;
var m = new Module();
var code = configParams + 'module.exports.config_paths=config_paths;module.exports.config_shim=config_shim;';
m._compile(code, 'tmpParams.js');
var dynamicParam = m.exports;
var config_paths = dynamicParam.config_paths;
var config_shim = dynamicParam.config_shim;

// 拼接buildjs内容
var str = "({";
str += "appDir:'../',";
str += "baseUrl:'../../',";
str += "dir:'./dist',";

var modules = [];
var excludeArray = ['jquery', 'mscore', 'select2'];
// 需要生成的模块：数组
for (var key in config_paths) {
    var tmpPath = config_paths[key];
    // 只有pages中页面的js需要合并，并排除掉api
    if (tmpPath.indexOf('/js/pages/') > 0 && tmpPath.indexOf('/js/pages/api/') === -1) {
        var tmpObj = {
            name: key,
        };
        if (!(key === "base")) {
            tmpObj.exclude = excludeArray;
        }
        modules.push(tmpObj);
    }
}

str += "modules:" +JSON.stringify(modules) + ",";
str += "fileExclusionRegExp: /^(r|build)\.js$/,";
str += "optimize:'none',";//uglify
str += "optimizeCss: 'standard',";
str += "removeCombined: true,";
str += "paths:" + JSON.stringify(config_paths) + ",";
str += "shim:" + JSON.stringify(config_shim) + ",";
str += "})";

fs.writeFile("msbuild.js", str, function (err) {
    if (err) {
        return console.log(err);
    }
    console.log("The build file was build!");
});
```