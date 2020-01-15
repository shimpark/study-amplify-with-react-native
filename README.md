<!-- markdownlint-disable -->
# AWSKRUG Community Day 2020 Amplify Demo
## Step
1. if you don't have expo, try this
    ```sh
    $ npm install -g expo-cli
    ```
    and follow other process refer to [this expo docs](https://docs.expo.io/versions/v36.0.0/get-started/installation/#installing-expo-cli)
1. `$ expo init`
   1. select `blank` in `Managed workflow`
   2. enter the project's `name` and `slug`. you can type `Tab` and `Shift + Tab` key to switch the cursor
   3. type `Y` to install expo project's dependency

    ```
    ? Choose a template: (Use arrow keys)
      ----- Managed workflow -----
    ❯ blank                 a minimal app as clean as an empty canvas 
      blank (TypeScript)    same as blank but with TypeScript configuration 
      tabs                  several example screens and tabs using react-navigation 
      ----- Bare workflow -----
      minimal               bare and minimal, just the essentials to get you started
      minimal (TypeScript)  same as minimal but with TypeScript configuration 

    ? Please enter a few initial configuration values.
      Read more: https://docs.expo.io/versions/latest/workflow/configuration/ › 100% completed
      {
        "expo": {
          "name": "JustApp",
          "slug": "awskrug-amplify-just-app"
        }
      }

    ? Yarn v1.21.1 found. Use Yarn to install dependencies? (Y/n) Yes

    ...
    yarn install v1.21.1
    installing proess...
    ...

    ✨  Done in 30.45s.

    Your project is ready at /Users/****/JustApp

    To get started, you can type:

      cd JustApp
      yarn start
    ```
1. install `react-navigation`
    ```sh
    $ expo install react-navigation react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context
    ```
1. install `react-navigation-stack` for Stack Navigation
    ```sh
    $ yarn add react-navigation-stack @react-native-community/masked-view
    ```
3. Expo Debugging
      ```sh
      expo start --android
      ```
1. amplify init
    ```
    $ amplify init

    Scanning for plugins...
    Plugin scan successful
    Note: It is recommended to run this command from the root of your app directory
    ? Enter a name for the project "JustApp"
    ? Enter a name for the environment "dev"
    ? Choose your default editor: "Visual Studio Code"
    ? Choose the type of app that you're building javascript
    Please tell us about your project
    ? What javascript framework are you using "react-native"
    ? Source Directory Path:  "src"
    ? Distribution Directory Path: "/" (just Enter)
    ? Build Command:  "npm run-script build" (just Enter)
    ? Start Command: "npm run-script start" (just Enter)
    Using default provider  awscloudformation

    For more information on AWS Profiles, see:
    https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html

    ? Do you want to use an AWS profile? (Y/n) "Y"

    ? Please choose the profile you want to use "{your profile name}"
    Adding backend environment dev to AWS Amplify Console app: d2egeeayc0vvkl
    ⠦ Initializing project in the cloud...

    CREATE_IN_PROGRESS amplify-justapp-dev-234651 AWS::CloudFormation::Stack Tue Jan 14 2020 23:46:54 GMT+0900 (Korean Standard Time) User Initiated             
    CREATE_IN_PROGRESS DeploymentBucket           AWS::S3::Bucket            Tue Jan 14 2020 23:46:57 GMT+0900 (Korean Standard Time) 
    
    ...

    ✔ Initialized provider successfully.
    Initialized your environment successfully.

    Your project has been successfully initialized and connected to the cloud!
    ```
2. amplify add api
   - authorization type: `API key`
   - edit `schema.graphql` with `@model` directive
    ```
    $ amplify add api

    ? Please select from one of the below mentioned services: GraphQL
    ? Provide API name: "justapp"
    ? Choose the default authorization type for the API "API key"
    ? Enter a description for the API key: ""
    ? After how many days from now the API key should expire (1-365): "7"
    ? Do you want to configure advanced settings for the GraphQL API "No, I am done."
    ? Do you have an annotated GraphQL schema? "No"
    ? Do you want a guided schema creation? "Yes"
    ? What best describes your project: "One-to-many relationship (e.g., “Blogs” with “Posts” and “Comments”)"
    ? Do you want to edit the schema now? (Y/n) "Y"

    Please edit the file in your editor: "/Users/{your path}/JustApp/amplify/backend/api/justapp/schema.graphql"

    ? Press enter to continue 
    ```
   - schema.graphql
      ```gql
      type S3Object {
        bucket: String!
        region: String!
        key: String!
      }

      type Post @model {
        id: ID!
        title: String!
        content: String!
        image: S3Object!
        like: Boolean!
        comments: [Comment] @connection(name: "PostComments", sortField: "createdAt")
        createdAt: AWSTimestamp!
        updatedAt: AWSTimestamp!
      }

      type Comment @model {
        id: ID!
        content: String
        post: Post @connection(name: "PostComments", sortField: "createdAt")
        createdAt: AWSTimestamp!
        updatedAt: AWSTimestamp!
      }

      ```

    - press `Enter` Key in cli and creating `API`'s step continues...
      ```
      The following types do not have '@auth' enabled. Consider using @auth with @model
          - Blog
          - Post
          - Comment
      Learn more about @auth here: https://aws-amplify.github.io/docs/cli-toolchain/graphql#auth 

      GraphQL schema compiled successfully.

      Edit your schema at /Users/{your path}/JustApp/amplify/backend/api/justapp/schema.graphql or place .graphql files in a directory at /Users/{your path}/JustApp/amplify/backend/api/justapp/schema
      Successfully added resource justapp locally
      ```
3. amplify mocking and testing
   - insert some data with using GraphQL's `mutation` query
4. install **amplify framework**
    - cli
    ```
    $ yarn add aws-amplify
    $ yarn add aws-amplify-react-native
    ```
    - App.js
    ```js
    import Amplify from 'aws-amplify';
    import config from './aws-exports’;

    Amplify.configure(config);
    ```
5. make `/src` folder and coding
   - `/src/component`
   - `/src/style`
   - `/src/screen`
   - `/src/navigator`
     - Stack Navigator: `stack.js`
       - Main: **Main.js**
       - CreatePost: **CreatePost.js**
     - Switch Navigator: `switch.js`
       - AuthLoading: **AuthLoadingScreen.js**
       - Main: **Stack Navigator**
       - Auth: **Auth.js**
6. add code **Amplify's** `API` Moule in expo
   - amplify add codegen
      ```
      $ amplify add codegen

      ? Choose the code generation language target "javascript"
      ? Enter the file name pattern of graphql queries, mutations and subscriptions "src/graphql/**/*.js"
      ? Do you want to generate/update all possible GraphQL operations - queries, mutations and subscriptions "Yes"
      ? Enter maximum statement depth [increase from default if your schema is deeply nested] "2"
      ✔ Generated GraphQL operations successfully and saved at src/graphql
      ```
   - import `API` and code with created `AppSync API`
7. execute `amplify mock` & `expo start`
   - get connection between `amplify's localhost api` and `expo debugging`
8. modify `amplify framework's API` code in `expo`
   - mutation, subscription
9. re-configure graphql query `maximum statement depth`
    ```
    $ amplify configure codegen

    ? Choose the code generation language target javascript
    ? Enter the file name pattern of graphql queries, mutations and subscriptions src/grap
    hql/**/*.js
    ? Enter maximum statement depth [increase from default if your schema is deeply nested
    ] 3

    $ amplify codegen
    ✔ Generated GraphQL operations successfully and saved at src/graphql
    ```
10. amplify add auth
   - add `@auth` directive in `amplify/backend/api/justapp/schema.graphql`
11. amplify update api
   - Change authorization type `API Key` -> `Amazon Cognito User Pool`
   - execute updated schema to type Amplify CLI
      ```
      $ amplify api update
      $ amplify codegen
      ```
12. amplify mock
   - check added `cognito_user_pool` Auth Type in Amplify's API Mock console
13. import Amplify's `Auth` module
    - using `withAuthenticator`
    - (if possible) use `I18n` util
14. amplify add storage
    - schema.graphql에서 S3Object 타입 추가하기
    - 변경된 schema update하기
      ```
      $ amplify api update
      $ amplify codegen
      ```
15. expo에서 amplify storage import해서 코딩하기
    - expo install expo-image-picker 한 이후 코딩
16. 자기 id가 코멘트에 언급되었는지(좋아요가 눌렸는지) subscription으로 확인하기
    - Amplify's API의 `subscription` query로 코딩하기
17. amplify mock & expo start
    - withAuthenticator로 로그인하고 이미지 업로드 해보기
18. amplify add analytics
    - expo에서 analytics import해서 코딩하기
19. expo start & check analytics on AWS console
20. amplify delete

## Build Standalone App

### Reference
[Building Standalone Apps](https://docs.expo.io/versions/v36.0.0/distribution/building-standalone-apps/)

### Step
- add prop in `app.json`
  ```json
    "android": {
      "package": "com.awskrug.justapp"
    }
  ```
- `expo build:android -t apk`
- `expo fetch:android:keystore` and backup your keystore to a safe location
