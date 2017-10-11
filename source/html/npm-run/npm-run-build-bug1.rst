

npm run build 出错情况1
-----------------------

no such file or directory
^^^^^^^^^^^^^^^^^^^^^^^^^

::

	[jlch@check ktraining]$ npm run build

	> line@1.0.0 build /home/jlch/registry/ktraining
	> node build/build.js

	⠸ building for production...
	Starting to optimize CSS...
	Processing static/css/app.d0d16c3b538edb8384cf55d5f92bcddd.css...
	Processed static/css/app.d0d16c3b538edb8384cf55d5f92bcddd.css, before: 74052, after: 71485, ratio: 96.53%
	Hash: 004d38f2752eb4dd3ca6
	Version: webpack 2.7.0
	Time: 56052ms
													  Asset       Size  Chunks                    Chunk Names
										 static/iconfont.js    38.9 kB          [emitted]
								  static/img/bg.54503c9.png    99.2 kB          [emitted]
					  static/js/app.1302bf22af6be784678a.js    69.7 kB       1  [emitted]         app
				 static/js/manifest.83d402e7507a68529b24.js    1.51 kB       2  [emitted]         manifest
		static/css/app.d0d16c3b538edb8384cf55d5f92bcddd.css    71.5 kB       1  [emitted]         app
			   static/js/vendor.4e94ca5aec8a4e524a18.js.map    7.98 MB       0  [emitted]         vendor
				  static/js/app.1302bf22af6be784678a.js.map     393 kB       1  [emitted]         app
	static/css/app.d0d16c3b538edb8384cf55d5f92bcddd.css.map    92.3 kB       1  [emitted]         app
			 static/js/manifest.83d402e7507a68529b24.js.map    14.6 kB       2  [emitted]         manifest
												 index.html  820 bytes          [emitted]
										  static/address.js  243 bytes          [emitted]
										 static/flexible.js    3.49 kB          [emitted]
				   static/js/vendor.4e94ca5aec8a4e524a18.js     924 kB       0  [emitted]  [big]  vendor
									 static/css/clearAd.css  109 bytes          [emitted]
									  static/img/fsRank.png    2.82 kB          [emitted]
										  static/img/bg.png    99.2 kB          [emitted]
									   static/img/kline.png    19.2 kB          [emitted]
									   static/img/kLine.png    3.75 kB          [emitted]
									   static/img/kRank.png    2.42 kB          [emitted]
									 static/img/records.png    3.25 kB          [emitted]
									  static/img/record.png    19.2 kB          [emitted]
									   static/img/share.png    19.7 kB          [emitted]
								   static/img/timeShare.png    3.12 kB          [emitted]
											static/users.js   44 bytes          [emitted]

	ERROR in ./~/_css-loader@0.28.7@css-loader?{"minimize":true,"sourceMap":true}!./~/_sass-loader@6.0.6@sass-loader/lib/loader.js?{"sourceMap":true}!./src/css/base.scss
	Module build failed: Error: ENOENT: no such file or directory, scandir '/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/vendor'
		at Object.fs.readdirSync (fs.js:862:18)
		at Object.getInstalledBinaries (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/extensions.js:124:13)
		at foundBinariesList (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:20:15)
		at foundBinaries (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:15:5)
		at Object.module.exports.missingBinary (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:45:5)
		at module.exports (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/binding.js:15:30)
		at Object.<anonymous> (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/index.js:14:35)
		at Module._compile (module.js:573:32)
		at Object.Module._extensions..js (module.js:582:10)
		at Module.load (module.js:490:32)
		at tryModuleLoad (module.js:449:12)
		at Function.Module._load (module.js:441:3)
		at Module.require (module.js:500:17)
		at require (internal/module.js:20:19)
		at Object.<anonymous> (/home/jlch/registry/ktraining/node_modules/_sass-loader@6.0.6@sass-loader/lib/loader.js:3:14)
		at Module._compile (module.js:573:32)
	 @ ./src/css/base.scss 4:14-180
	 @ ./src/main.js

	ERROR in ./src/css/base.scss
	Module build failed: ModuleBuildError: Module build failed: Error: ENOENT: no such file or directory, scandir '/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/vendor'
		at Object.fs.readdirSync (fs.js:862:18)
		at Object.getInstalledBinaries (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/extensions.js:124:13)
		at foundBinariesList (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:20:15)
		at foundBinaries (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:15:5)
		at Object.module.exports.missingBinary (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/errors.js:45:5)
		at module.exports (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/binding.js:15:30)
		at Object.<anonymous> (/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/lib/index.js:14:35)
		at Module._compile (module.js:573:32)
		at Object.Module._extensions..js (module.js:582:10)
		at Module.load (module.js:490:32)
		at tryModuleLoad (module.js:449:12)
		at Function.Module._load (module.js:441:3)
		at Module.require (module.js:500:17)
		at require (internal/module.js:20:19)
		at Object.<anonymous> (/home/jlch/registry/ktraining/node_modules/_sass-loader@6.0.6@sass-loader/lib/loader.js:3:14)
		at Module._compile (module.js:573:32)
		at runLoaders (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/NormalModule.js:192:19)
		at /home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:364:11
		at /home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:170:18
		at loadLoader (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/loadLoader.js:27:11)
		at iteratePitchingLoaders (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:169:2)
		at iteratePitchingLoaders (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:165:10)
		at /home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:173:18
		at loadLoader (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/loadLoader.js:36:3)
		at iteratePitchingLoaders (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:169:2)
		at runLoaders (/home/jlch/registry/ktraining/node_modules/_loader-runner@2.3.0@loader-runner/lib/LoaderRunner.js:362:2)
		at NormalModule.doBuild (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/NormalModule.js:179:3)
		at NormalModule.build (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/NormalModule.js:268:15)
		at Compilation.buildModule (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/Compilation.js:146:10)
		at moduleFactory.create (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/Compilation.js:433:9)
		at factory (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/NormalModuleFactory.js:253:5)
		at applyPluginsAsyncWaterfall (/home/jlch/registry/ktraining/node_modules/_webpack@2.7.0@webpack/lib/NormalModuleFactory.js:99:14)

	  Build complete.

	  Tip: built files are meant to be served over an HTTP server.
	  Opening index.html over file:// won't work.

	[jlch@check ktraining]$ 
	
解决

这个看出来是 `Error: ENOENT: no such file or directory, scandir '/home/jlch/registry/ktraining/node_modules/_node-sass@4.5.3@node-sass/vendor'`, 
node-sass 少了 vendor, 那就是说，我们要把这个 node-sass 安装完整才行。

那就来吧 

::

	cd node_modules/
	ls node-sass/
	cd node-sass/
	ls
	npm test  ## 这里先做一个 测试，应该来说，此时是会报错的。
	npm install
	npm test  ## 好了，安装成功后，此时不应该报错。
	cd ../../../ktraining/ ## 回头重新 build 吧。
	npm run build

这下子成功啦。





