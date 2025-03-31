## Look It Up

* [Typescript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

* [W3 HTML](https://www.w3schools.com/)
* [W3 CSS](https://www.w3schools.com/css/default.asp)
* [W3 Javascript](https://www.w3schools.com/js/default.asp)
* [W3 Typescript](https://www.w3schools.com/typescript/index.php)


## Setting up a Typescript projects

### Installing Node.js and Typescript

We'll install Typescript with `npm` which is part of the `Node.js` package.

* go to the [node.js](https://nodejs.org/en/) page. 
* Download the latest `node.js` and follow the package installation instructions.
* go to a bash shell and run the command 
```npm install -g typescript```
The `-g` flag means to install it globally on the system.  
* if you get an error that the installation can't open or create a directory, then you'll have to run it with `sudo`.
```sudo npm install -g typescript```
and enter your computer login password.


### Setting up Project directory

* create a directory for your project
* add an `index.html` file.  You can initialize your `index.html` file by just typing `html` in it and then select `html:5` from the pull down.
* Add a `script` tag.

```
<script src="app.js" defer><script>
```
where `app.js` is the name of your javascript file.
* add an `app.ts` file
* in your project directory run the command
```npm init```
and answer all the questions with the default.
* in your project directory run the command
```npm install --save-dev lite-server```
this installs a local webserver to serve your pages locally on your machine.
* in your `package.json` go to the `scripts` section and add the entry
```"start": "lite-server"```
You may need to add a comma after the last entry. It's a list.
* run the command
```tsc --init```
* run the command
```tsc --watch```
* run the command
```npm start```
this will start the local webserver and open your page, or you can open a browser and enter the url `http://localhost:3000`. You can kill this server by typeing `ctr-C` in the shell.

### Typescript Compiler Configuration

- `sourceMap` (section 41) - If you turn on source maps the compiler will create files that are read by the console/debugger.  This allows you to see the typescipt files in the debugger and set break points.  This is helpful for debugging.
- `rootDir` and `outDir` (section 42) - This allows you to put your typescript files in a subfolder, possibly with nested subfolders that is the root directory.  Setting the `rootdir` tells typescript to only compile files in that folder.  You can also create a subfolder to hold all the generated javascript files in one place.  Setting the `outdir` folder will tell typescript where to place generated files.
- `strict` (section 44, 45) - There are a bunch of settings to turn on more strict compilation rule.  You can set them all at once by just setting `strict` to `true`.  Strict compilation is a way of reducing runtime errors. If you turn this on you will get more errors. There are additional settings that enforce various coding practices and rules that can make code easier to read and more reliable.  If you turn these on you will get more errors.
- **Debugger for Chrome** works with VStudios IDE so you can debug your code in Visual Studio rather than relying entirely on the Dev Tools that come with your browser.  It's integrated into the IDE so you can see errors in the IDE. Also consider using **ESLint** and **Prettier** in VStudio.  Ask Pipeworks what they use and just use the same ones. These are all things you install from within VStudio.

### Set up a React Typescript Project

Create an empty folder for your project

Run the command 
```npx create-react-app my-app --template typescript```
to create a react app.

You can test your new app by running 
```npm start```
It will basically be an empty app.



### Adding jest to your React Typescript Project

In your `package.json` file set 
```"test": "jest",```



Install following packages
```npm install ts-jest ts-node @types/jest```


Add a jest config file called `jest.config.ts` to you project.

Here is a sample config file you can paste into `jest.config.ts`
```
import type { Config } from '@jest/types'
import exp from 'constants'

const config: Config.InitialOptions = {
    roots: [
        "<rootDir>/test",
        "<rootDir>/src"
    ],
    transform: {
        "^.+\\.tsx?$": "ts-jest"
    },
    setupFilesAfterEnv: [
        "@testing-library/jest-dom/extend-expect"
    ],
    testRegex: "(/__tests__/.*|(\\.|/)(test|spec))\\.tsx?$",
    moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
    testEnvironment: "jsdom",
    collectCoverage: true,
    collectCoverageFrom: [
        'src/**/*.{ts,tsx}'
    ]
}

export default config;
```


Run your tests with the command
```npm test```

### Create React Typescript Project From the Udemy Tutorial

[React Typescript Set Up](https://create-react-app.dev/docs/adding-typescript/)



