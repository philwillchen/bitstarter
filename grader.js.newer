#!/usr/bin/env node

var fs = require('fs');

var program = require('commander');
var cheerio = require('cheerio');
var rest = require('restler');
var HTMLFILE_DEFAULT = "index.html";
var CHECKSFILE_DEFAULT = "checks.json";

var assertFileExists = function(infile) {
    var instr = infile.toString();
    if(!fs.existsSync(instr)) {
        console.log("%s does not exist. Exiting.", instr);
        process.exit(1); // http://nodejs.org/api/process.html#process_process_exit_code
    }
    return instr;
};


var loadChecks = function(checksfile) {
    return JSON.parse(fs.readFileSync(checksfile));
};


var checkHtmlFile = function($, checksfile) {
    var checks = loadChecks(checksfile).sort();
    var out = {};
    for(var ii in checks) {
        var present = $(checks[ii]).length > 0;
        out[checks[ii]] = present;
    }
    console.log(JSON.stringify(out, null, 4));
};


var loadHtmlFile = function(htmlfile, checksfile) {
    if (htmlfile.substr(0, 4) === 'http') {
        rest.get(htmlfile).on('complete', function(result) {
            $ = cheerio.load(result);
            checkHtmlFile($, checksfile);
        });
    } else {
        $ = cheerio.load(fs.readFileSync(htmlfile));
        checkHtmlFile($, checksfile);
    }
};

var clone = function(fn) {
    // Workaround for commander.js issue.
    // http://stackoverflow.com/a/6772648
    return fn.bind({});
};

if(require.main == module) {
    program
        .option('-c, --checks <check_file>', 'Path to checks.json', clone(assertFileExists), CHECKSFILE_DEFAULT)
        .option('-f, --file <html_file>', 'Path to index.html', clone(assertFileExists), HTMLFILE_DEFAULT)
        .option('-u, --url <url>', 'URL')
        .parse(process.argv);
    if (program.url) {
      loadHtmlFile(program.url, program.checks);
    } else {
      loadHtmlFile(program.file, program.checks);
    }
} else {
    exports.loadHtmlFile = loadHtmlFile;
}
