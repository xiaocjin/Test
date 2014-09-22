
## 工具

#### Grunt

  >JavaScript世界的构建工具。在mean项目中用于集成各种工具（包括测试），达到自动化的目标。

#### Karma

  >Karma是一个基于Node.js的JavaScript测试执行过程管理工具（Test Runner）。该工具可用于测试所有主流Web浏览器，也可集成到CI（Continuous integration）工具，也可和其他代码编辑器一起使用。在mean中用于前端js代码的测试执行和管理。

#### Jasmine

  >Jasmine是一个用来编写Javascript测试的框架，它不依赖于任何其它的javascript框架，也不需要对DOM。在mean中用来编写前端Javascript（Angular）测试。
  
#### Mocha

  >Mocha 是一个运行在node.js和浏览器环境上的JavaScript测试框架。它由TJ所写，在mean中用来编写nodejs代码测试。
  
#### Should

## 集成

#### Gruntfile.js

    'use strict';
    
    var paths = {
      js: ['*.js', 'test/**/*.js', '!test/coverage/**', '!bower_components/**', 'packages/**/*.js', '!packages/**/node_modules/**', '!packages/contrib/**/*.js', '!packages/contrib/**/node_modules/**'],
      html: ['packages/**/public/**/views/**', 'packages/**/server/views/**'],
      css: ['!bower_components/**', 'packages/**/public/**/css/*.css', '!packages/contrib/**/public/**/css/*.css']
    };
    
    module.exports = function(grunt) {
    
      if (process.env.NODE_ENV !== 'production') {
        require('time-grunt')(grunt);
      }
    
      // Project Configuration
      grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        assets: grunt.file.readJSON('config/assets.json'),
        clean: ['bower_components/build'],
        watch: {
          js: {
            files: paths.js,
            tasks: ['jshint'],
            options: {
              livereload: true
            }
          },
          html: {
            files: paths.html,
            options: {
              livereload: true,
              interval: 500
            }
          },
          css: {
            files: paths.css,
            tasks: ['csslint'],
            options: {
              livereload: true
            }
          }
        },
        jshint: {
          all: {
            src: paths.js,
            options: {
              jshintrc: true
            }
          }
        },
        uglify: {
          core: {
            options: {
              mangle: false
            },
            files: '<%= assets.core.js %>'
          }
        },
        csslint: {
          options: {
            csslintrc: '.csslintrc'
          },
          src: paths.css
        },
        cssmin: {
          core: {
            files: '<%= assets.core.css %>'
          }
        },
        nodemon: {
          dev: {
            script: 'server.js',
            options: {
              args: [],
              ignore: ['node_modules/**'],
              ext: 'js,html',
              nodeArgs: ['--debug'],
              delayTime: 1,
              cwd: __dirname
            }
          }
        },
        concurrent: {
          tasks: ['nodemon', 'watch'],
          options: {
            logConcurrentOutput: true
          }
        },
        mochaTest: {
          options: {
            reporter: 'spec',
            require: [
              'server.js',
              function() {
                require('meanio/lib/util').preload(__dirname + '/packages/**/server', 'model');
              }
            ]
          },
          src: ['packages/**/server/tests/**/*.js']
        },
        env: {
          test: {
            NODE_ENV: 'test'
          }
        },
        karma: {
          unit: {
            configFile: 'karma.conf.js'
          }
        }
      });
    
      //Load NPM tasks
      require('load-grunt-tasks')(grunt);
    
      //Default task(s).
      if (process.env.NODE_ENV === 'production') {
        grunt.registerTask('default', ['clean', 'cssmin', 'uglify', 'concurrent']);
      } else {
        grunt.registerTask('default', ['clean', 'jshint', 'csslint', 'concurrent']);
      }
    
      //Test task.
      grunt.registerTask('test', ['env:test', 'mochaTest', 'karma:unit']);
    
      // For Heroku users only.
      // Docs: https://github.com/linnovate/mean/wiki/Deploying-on-Heroku
      grunt.registerTask('heroku:production', ['cssmin', 'uglify']);
    };

#### karma.conf.js

    'use strict';
    
    // Karma configuration
    module.exports = function(config) {
      var _ = require('lodash'),
        basePath = '.',
        assets = require(basePath + '/config/assets.json');
    
      config.set({
    
        // base path, that will be used to resolve files and exclude
        basePath: basePath,
    
        // frameworks to use
        frameworks: ['jasmine'],
    
        // list of files / patterns to load in the browser
        files: _.flatten(_.values(assets.core.js)).concat([
          'packages/*/public/*.js',
          'packages/*/public/*/*.js'
        ]),
    
        // list of files to exclude
        exclude: [],
    
        // test results reporter to use
        // possible values: 'dots', 'progress', 'junit', 'growl', 'coverage'
        reporters: ['progress', 'coverage'],
    
        // coverage
        preprocessors: {
          // source files that you want to generate coverage for
          // do not include tests or libraries
          // (these files will be instrumented by Istanbul)
          'packages/*/public/controllers/*.js': ['coverage'],
          'packages/*/public/services/*.js': ['coverage']
        },
    
        coverageReporter: {
          type: 'html',
          dir: 'test/coverage/'
        },
    
        // web server port
        port: 9876,
    
        // enable / disable colors in the output (reporters and logs)
        colors: true,
    
        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_INFO,
    
        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,
    
        // Start these browsers, currently available:
        // - Chrome
        // - ChromeCanary
        // - Firefox
        // - Opera
        // - Safari (only Mac)
        // - PhantomJS
        // - IE (only Windows)
        browsers: ['PhantomJS'],
    
        // If browser does not capture in given timeout [ms], kill it
        captureTimeout: 60000,
    
        // Continuous Integration mode
        // if true, it capture browsers, run tests and exit
        singleRun: true
      });
    };

#### 小结

>Jasmine做单元测试，Karma自动化完成单元测试，Grunt启动Karma统一项目管理，Yeoman最后封装成一个项目原型模板，npm做nodejs的包依赖管理，bower做javascript的包依赖管理。
