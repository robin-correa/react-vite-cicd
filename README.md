# React + TypeScript + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

## Expanding the ESLint configuration

If you are developing a production application, we recommend updating the configuration to enable type aware lint rules:

- Configure the top-level `parserOptions` property like this:

```js
export default {
  // other rules...
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: ['./tsconfig.json', './tsconfig.node.json'],
    tsconfigRootDir: __dirname,
  },
}
```

- Replace `plugin:@typescript-eslint/recommended` to `plugin:@typescript-eslint/recommended-type-checked` or `plugin:@typescript-eslint/strict-type-checked`
- Optionally add `plugin:@typescript-eslint/stylistic-type-checked`
- Install [eslint-plugin-react](https://github.com/jsx-eslint/eslint-plugin-react) and add `plugin:react/recommended` & `plugin:react/jsx-runtime` to the `extends` list

## AWS S3 Static - Bucket Policy JSON

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::<bucket-name>",
                "arn:aws:s3:::<bucket-name>/*"
            ]
        }
    ]
}
```

## AWS CodeBuild - Environment Variable

- Add environment variable: `S3_BUCKET` and set the value to your corresponding bucket name.
  - Sample:
    - Key: `S3_BUCKET`
    - Value: `s3://react-vite-cicd`
    - Type: `PLAINTEXT`

- Buildspec file:

```yml
version: 0.2

phases:
  install:
    commands:
      - echo "Installing dependencies..."
      - npm install

  build:
    commands:
      - echo "Building Vue app..."
      - npm run build
      
  post_build:
    commands:
      - echo "Uploading / Syncing to S3"
      - aws s3 sync ./dist $S3_BUCKET
```

## AWS CodePipeline

- Add `Build` stage
  - Set `Trigger` to `no filter`.
- Skip `Deploy` stage
- Save changes