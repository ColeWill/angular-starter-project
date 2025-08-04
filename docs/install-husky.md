[husky install docs](https://typicode.github.io/husky/get-started.html)

1. Install it as a dev-dependency
   `npm i --save-dev husky`

2. Run the husky binary to init
   `npx husky init`

3. Add a new hook, this adds the name of the script for package.json and a line to run it in the husky hook you define, 'npm test' is the script name, and 'pre-commit' is the file in .husky/pre-commit

`echo "npm test" > .husky/pre-commit`
