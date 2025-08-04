[How to install Vitest in Angular courtesy of Duncan Faulkner](https://anglebrackets-dev.medium.com/introduction-to-vitest-and-angular-9a951aa1ec17)

The Angular team deprecated Karma a few versions ago and are currently working on ways to provide an alternative 3rd party unit testing frameworks. Currently the options talked about so far are Web Test Runner (likely to be the default), this is a browser based unit test runner similar in many ways to Karma. The other option being discussed is Jest, this can be installed now but can be a little tricky sometimes to get set up and some known issues when Angular is set up to use ES builds (the default in Angular 18 onward).

The Angular team are looking at using the CLI for the installation process of these unit testing frameworks, similar to when selecting a CSS framework when creating a new project. This is all still very early on in the development cycle and may be a little way off before it becomes developer preview (currently still experimental) and no mention (as yet) of how this would work with an existing project.

Now that newer versions of Angular use Vite as a development build server and the default in Angular 18, it is now possible to use Vitest in our Angular projects. The installation process is straight forward to set up and feels less problematic than setting up Jest from my recent experience. The great news here is that Vitest has a similar syntax to Karma and Jest, so the learning curve should be fairly small.

For more information on the build migration in Angular you can check the documentation at: [Angular build system migration](https://angular.dev/tools/cli/build-system-migration)

In this post I’m going to walk through setting up and configuring Vitest in an Angular 18 application and replacing Karma . I won’t be writing unit tests in this post, I’ll leave that for another post.

I’m going to start a new project for this post but this will also work on an existing project.

In a directory type the following in the terminal:

ng new my-vitest-app && cd my-vitest-app && npm i
This will create a new Angular application called my-vitest-app, it'll change into that directory and then run npm install with our application created we can now remove Karma.

From the terminal, type:

npm uninstall karma karma-chrome-launcher karma-coverage karma-jasmine karma-jasmine-html-reporter
We don’t need to uninstall @types/jasmine and jasmine-core as Vitest uses Jasmine under the hood, we can use it and describe from Jasmine so we don't need to import anything in our test files. It is possible to use it and describe from Vitest but in doing so we won't be able to use functions like fakeAysnc in our unit tests.

The easiest way to get Vitest installed in our project is to use a plugin and AnalogJS has just what we need to setup and configure Vitest for us.

From the terminal type:

npm i @analogjs/platform -D
Followed by:

ng g @analogjs/platform:setup-vitest --project my-vitest-app
This package will install the following files:

"devDependencies": {
// other files removed for brevity
"@analogjs/platform": "^1.9.0",
"@analogjs/vite-plugin-angular": "^1.9.0",
"@analogjs/vitest-angular": "^1.9.0",
"@nx/vite": "~19.8.2",
"@vitest/coverage-v8": "^2.1.3",
"@vitest/ui": "^2.1.3",
"vite": "^5.4.9",
"vite-tsconfig-paths": "^4.2.0",
"vitest": "^2.1.3"
}
At the time of writing the @analogjs library installs @nx/vite 19.8.2, Brandon Roberts (AnalogJS creator) has released a beta version that supports @nx/vite 20.0.3.

This @analog package should install 2.1.3 of vitest, @vitest/ui and @vitest/coverage-v8, if it installs 1.6.0 then you will need to uninstall these packages and reinstall them using:

npm i @vitest/coverage-v8 @vitest/ui vitest@latest -D
Once complete the following files will be generated:

// vite.config.mts
/// <reference types="vitest" />
import angular from '@analogjs/vite-plugin-angular';
import { defineConfig } from 'vite';

// https://vitejs.dev/config/
export default defineConfig(({ mode }) => {
return {
plugins: [
angular(),
],
test: {
globals: true,
environment: 'jsdom',
setupFiles: ['src/test-setup.ts'],
include: ['**/*.spec.ts'],
reporters: ['default'],
},
define: {
'import.meta.vitest': mode !== 'production',
},
};
});
vite.config.mts will be in the root of the project, this sets up the configuration for Vitest, what environment to use, the default is jsdom, you can change this to happy-dom (you will need to install happy-dom). The test-setup.ts will be in the src directory and this sets up the test environment.

import '@analogjs/vitest-angular/setup-zone';
import {
BrowserDynamicTestingModule,
platformBrowserDynamicTesting,
} from '@angular/platform-browser-dynamic/testing';
import { getTestBed } from '@angular/core/testing';

getTestBed().initTestEnvironment(
BrowserDynamicTestingModule,
platformBrowserDynamicTesting()
);
If you are using azoneless application then the test-setup.ts will be slightly different:

import '@analogjs/vitest-angular/setup-snapshots';

import {
BrowserDynamicTestingModule,
platformBrowserDynamicTesting,
} from '@angular/platform-browser-dynamic/testing';
import { getTestBed } from '@angular/core/testing';

getTestBed().initTestEnvironment(
BrowserDynamicTestingModule,
platformBrowserDynamicTesting()
);
In angular.json the test section will be replaced with:

"test": {
"builder": "@analogjs/vitest-angular:test"
}
And finally the tsconfig.spec.json will be update to:

{
"extends": "./tsconfig.json",
"compilerOptions": {
"outDir": "./out-tsc/spec",
"types": [
"jasmine",
"vitest/globals" // added
],
"target": "es2016" // added
},
"include": [
"src/**/*.spec.ts",
"src/**/*.d.ts"
],
"files": [
"src/test-setup.ts" // added
]
}
To run Vitest tests we need to change the scripts section, in our applications package.json file add:

{
// other scripts
"test" : "vitest"
}

// we could also use this, if you are not the running vitest command
{
"test" : "ng test --watch"
}
Using npm run test will run all of our test files our project.
