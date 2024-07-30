# Genesis Docs
This is official documentation for Genesis-Embodied-AI project.

## Usage
### Running the development server
To preview your changes as you edit the files, you can run a local development server that will serve your website and reflect the latest changes.

First install node.js following
[this link](https://nodejs.org/en/download/package-manager)



Then run your website locally:
```
cd docs
npm start
```
By default, a browser window will open at http://localhost:3000.


### How to build the website in github.io (already finish, developer can ignore)
You can follow this [link](https://emmachan2021.github.io/docs/tech/docusaurus-github)


## API doc genetation example:
First, input the following prompt to GPT-4:
```
This is the python function API code: 

>>>YOUR FUNCTION CODE HERE<<<
    
Please write API doc in mdx file format, like below example:

# API 1

import APITable from '@site/src/components/APITable';

:::info
info xxxxx
:::
:::tip
tip xxxxx
:::
:::danger Breaking changes in minor versions
xxxx
:::
:::note
note xxxx 
:::

## Overview {#overview}

API 1 contains xxxx
- Type: string | undefined

The API 1 file supports:

- [**a**](https://flaviocopes.com/es-modules/)
- [**b**](https://flaviocopes.com/commonjs/)
- [**c**](./example1/README.mdx)

Examples:

js title="API 1"
export default {
  title: 'xxx',
  url: 'https://xxxx.io',
  // your site config ...
};

js title="API 1"
export default async function createConfigAsync() {
  return {
    title: 'xxx',
    url: 'https://xxx.io',
    // your site config ...
  };
}
```

Then copy-paste the result to the mdx file in `website/docs/api` folder and do some refinement.


To Update Website, you should run as following:
```
npm run build
npm run deploy
```