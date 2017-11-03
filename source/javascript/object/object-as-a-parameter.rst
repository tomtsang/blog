========================
object as a parameter
========================

当一个 object as a parameter 时，一定要注意了。

很容易忽略掉这个object变修改，或没有被修改。

可以采用复制的方式。


::

    [cdn@test_240 test]$ cat testmap.js 
    var mapa = new Map();
    var obj = {key1:2,key2:2};
    mapa.set("name",obj);
    obj.key3 = 3;
    console.log(mapa.get("name"));
    console.log(obj);
    [cdn@test_240 test]$ node testmap.js 
    { key1: 2, key2: 2, key3: 3 }
    { key1: 2, key2: 2, key3: 3 }
    [cdn@test_240 test]$ cat funcparam.js 
    var obj = {};
    function a(pobj){
    var t ={a:3};
    pobj = t;
    pobj.aa = 'aa';
    console.log("t :",t);
    console.log("pobj:", pobj);
    }
    function b(pobj){
    pobj.b = '5'
    }
    a(obj);
    console.log(obj);
    b(obj);
    console.log(obj);
    [cdn@test_240 test]$ node funcparam.js 
    t : { a: 3, aa: 'aa' }
    pobj: { a: 3, aa: 'aa' }
    {}
    { b: '5' }
    [cdn@test_240 test]$ 

