var args = require("./args");
var returnedNull = args.RETURNEDNUL;

/**
 * 生成函数的默认执行空间，这里会实现以下几个函数
 **/

//查找值
//突然想起来了，这样的一个查找值的函数应该是相当复杂的，
//除了在 g， l中查找以外，还应该查找 a.b a["b"], a[1] 这样类型的数据，
//烦死了！
//好在现在不用写
var findValue = function(g, l, name, isGlobal){
    //判断 name 的类型， 并将 name 拆分成 list ，传入 _findValue, 得到返回值
    var list = [];
    _getList(name, list);
    var result = _findValue(g, l, list, 2);
    if(result[0] == true) return result[1];
    return returnedNull;
};

//必须保证输入是合法的
//默认全部是 str 使用 [] 方式获取值
//先保留上面的写法
var _getList = function(name, list, type){
    type = type || "string";
    var dotPos = name.indexOf(".");
    var braPos = name.indexOf("[");
    if(dotPos > 0 || braPos >0){
        if(dotPos < braPos && dotPos != -1){  //必须使用 dotPos
            var newName = name.substring(0, dotPos - 1);
            list.push(["str", newName]);
            _getList(name.substr(dotPos + 1), list);
        }else if(branPos == -1){                   // dotPos >= braPos
            var newName = name.substring(0, braPos - 1);
            list.push(["str", newName]);
            name = name.substring(braPos + 1);
            newName = name.substring(1, name.indexOf("]") - 1);
            list.push(["str", newName]);
            _getList(name.substring(name.indexOf("]") + 1), list);
        }
    }else{
        if(type != "string") list.push(["str", name]);
        else list.push(["num", name]);
    }
};

/**
 * @param g
 * @param l
 * @param list
 * @param isGlobal  0: false, 1: true, 2: not sure, default: 2
 * @private
 */
var _findValue = function(g, l, list, isGlobal){
    isGlobal = isGlobal || 2;
    if(isGlobal == 0){
        //操作 l 中的数据
    }else if(isGlobal == 1){
        //操作 g 中的数据
    }else{
        //先 l，  后 g
        var result = _findValue(g, l, list, 0);
        if(result[0] == true) return result;
        return _findValue(g, l, list, 1);
    }
};

//对象的深拷贝, 先这样写着，以后再实现自己的模板
var  copyLocal = function(l){
    return JSON.parse(JSON.stringify(l));
};

//对象的运行函数
var runGeneratedFunction = function(g, l, fun){
    return eval(fun);
};

module.exports = {
    runGeneratedFunction: runGeneratedFunction,
};
