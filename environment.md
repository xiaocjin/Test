#测试环境配置

##安装全局环境

    npm install -g karma

##配置Gruntfile

    module.exports = function(grunt) {
    
      grunt.initConfig({
        uglify: {
          build: {
            src: ['js/src.js'],
            dest: 'js/src.min.js'
          }
        },
    	markdown: {
    		all: {
    		  files: [
    			{
    			  expand: true,
    			  src: 'Advanced+Express+Web+Application+Development/*.md',
    			  dest: 'docs/html/',
    			  ext: '.html'
    			}
    		  ],
    		  options: {
    			template: 'template/index.html',
    			markdownOptions: {
    			  gfm: true,
    			  codeLines: {
    				before: '<span>',
    				after: '</span>'
    			  }
    			}
    		  }
    		}
    	},
    	karma:{
    	  unit:{
    	    configFile:'karma.conf.js'
    	  }
    	}
      });
    
      grunt.loadNpmTasks('grunt-contrib-uglify');
      grunt.loadNpmTasks('grunt-contrib-watch');
      grunt.loadNpmTasks('grunt-markdown');
      grunt.loadNpmTasks('grunt-karma');
      grunt.registerTask('default', ['uglify','markdown','karma']);
    };

##配置karma.conf

    // Karma configuration
    // Generated on Fri Apr 25 2014 10:54:16 GMT+0800 (中国标准时间)
    
    module.exports = function(config) {
      config.set({
    
        // base path, that will be used to resolve files and exclude
        basePath: '',
    
    
        // frameworks to use
        frameworks: ['jasmine'],
    
    
        // list of files / patterns to load in the browser
        files: [
          'js/*.js'
        ],
    
    
        // list of files to exclude
        exclude: [
          'karma.conf.js'
        ],
    	
    	preprocessors : {'js/*.js': 'coverage'},
    
        // test results reporter to use
        // possible values: 'dots', 'progress', 'junit', 'growl', 'coverage'
        reporters: ['progress','coverage'],
    
    
        // web server port
        port: 9876,
    
    
        // enable / disable colors in the output (reporters and logs)
        colors: true,
    
    
        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_ERROR,
    
    	loggers:[{type: 'console'}],
        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,
    
    
        // Start these browsers, currently available:
        // - Chrome
        // - ChromeCanary
        // - Firefox
        // - Opera (has to be installed with `npm install karma-opera-launcher`)
        // - Safari (only Mac; has to be installed with `npm install karma-safari-launcher`)
        // - PhantomJS
        // - IE (only Windows; has to be installed with `npm install karma-ie-launcher`)
        browsers: ['Chrome'],
    
    
        // If browser does not capture in given timeout [ms], kill it
        captureTimeout: 60000,
    
    
        // Continuous Integration mode
        // if true, it capture browsers, run tests and exit
        singleRun: true,
    	coverageReporter: {
    		type : 'html',
    		dir : 'coverage/'
    	}
      });
    };


##在package.json添加引用模块

    "devDependencies": {
      "grunt": "^0.4.4",
      "grunt-contrib-uglify": "^0.4.0",
      "grunt-contrib-watch": "^0.6.1",
      "grunt-markdown": "^0.5.0",
      "grunt-karma": "^0.8.2",
      "grunt-contrib-concat": "^0.4.0",
      "grunt-contrib-jshint": "^0.10.0",
      "karma-jasmine": "^0.1.5",
      "karma-coverage": "^0.2.1",
      "karma-chrome-launcher": "^0.1.3"
    }

##jasmine html

    <html>
    <head>
      <title>Jasmine Spec Example</title>
    
      <link rel="shortcut icon" type="image/png" href="lib/jasmine-1.3.1/jasmine_favicon.png">
      <link rel="stylesheet" type="text/css" href="lib/jasmine-1.3.1/jasmine.css">
      <script type="text/javascript" src="lib/jasmine-1.3.1/jasmine.js"></script>
      <script type="text/javascript" src="lib/jasmine-1.3.1/jasmine-html.js"></script>
    
      <script type="text/javascript">
        ......
      </script>
    
      <script type="text/javascript">
        	
    	var jasmineEnv = jasmine.getEnv();
        var htmlReporter = new jasmine.HtmlReporter();
    		
        jasmineEnv.addReporter(htmlReporter);
    	  
    	jasmineEnv.specFilter = function(spec) {
    		return htmlReporter.specFilter(spec);
        };	  
    	
    	window.onload = function() {
    		jasmineEnv.execute();
        };
      </script>
    </head>
    
    <body>
    </body>
    </html>
