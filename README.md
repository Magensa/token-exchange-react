# Token Exchange Connect (PII) React Component
[![npm version](https://img.shields.io/npm/v/@magensa/token-exchange-react.svg?style=for-the-badge)](https://www.npmjs.com/package/@magensa/token-exchange-react "@magensa/token-exchange-react npm.js")  
React component for use with Token Exchange Connect.  
This component is designed to utilize TEConnect's PII (Personally Identifiable Information) utility.  
If you are looking for [TEConnect Payment](https://github.com/Magensa/te-connect-react) - the [README there](https://github.com/Magensa/te-connect-react) will get you started.  

# Prerequisites
This document will guide you through how to integrate Token Exchange Connect (PII) into your web application.  
While this code will get you up and running right away - in order to *__successfully__* create tokens - you will need a few items that are out of scope of this document. These prerequisites involve a valid [Magensa™](https://www.magensa.net) account.
  
For assistance, and more information on obtaining these prerequisites, please contact your Magensa™ representative, or [Magensa™ support staff](https://www.magensa.net/support.html).  
The prerequisites are:  
- A TEConnect Public Key (contains public credentials for Magensa™ Services), also referred to as a 'pubkey'.
    - As a note to interested developers who have not yet obtained a valid ```pubKey```- If you supply any valid Base64 encoded string - you can get the application up and running. So an example of a temp key to run the application (but not to successfully create tokens) would be: 
    ```javascript
    const tempPubKey = btoa("__publicKeyGoesHere__");
    ```
    - Once you obtain a valid ```pubKey``` - you will also have another set of credentials associated with your account.  The ```pubKey```, is not sensative - and is intended for use in your web application. The other set of credentials (Customer Code, Username, Password) *__are__* sensative, and should remain private.

- Pre-existing containers to store the tokens.
    - Each input box must have a valid container name (where the data entered into the input will be tokenized and stored). This container must be created by your account in advance (using your private credentials).
        - It's important to note that this container must be marked as "shared" upon creation. The "shared" allows the public credentials to use a container created by the private credentials. ```"containerTypeID": 1```  
    - Here is more information on [how to create containers using TokenV2 Web Service](https://www.magtek.com/content/documentationfiles/d998200451.pdf#page=18).
    - Bear in mind that every input will use the containerName supplied. You may choose to store all the tokens in the same container or seperately.

<br />

# Getting Started Token Exchange React PII
```
npm install @magensa/token-exchange @magensa/token-exchange-react
```
or
```
yarn add @magensa/token-exchange @magensa/token-exchange-react
```  

If you would prefer to let the code speak, below we have an [example implementation](#Example-Implementation)  
<br/>

## Step By Step

1. The first step is to build your `teInputsMap`. This map is essentially a selection of which inputs you would like displayed - along with the containerName for each input.  

TEConnect supports various different PII data points to render in your form. There are two different types of inputs available to choose from:
- [Managed Inputs](#Input-Boxes-Available-Managed-Inputs), which are input boxes that come loaded with validation, error handling, and input masks.
- [Generic Inputs](#Generic-Inputs), which are unvalidated inputs that can be used for various use-cases that the managed inputs do not account for.

Please see [this list below](#Input-Boxes-Available-Managed-Inputs) for all the currently available managed input boxes. In order to render a chosen input box, you must include the input object in the ```teInputsMap``` object supplied to the ```TokenExchange``` component.  
  

# Input Boxes Available (Managed Inputs)
| Input Name | Default Label | ```onChange``` Validation | ```onBlur``` Validation | Required Validation |
|:--:|:--:|:--:|:--:|:--:|
| firstName | First Name | none | ```(length > 0 && length < 256)``` | :heavy_check_mark: |
| middleName | Middle Name | none | none | |
| lastName | Last Name | none | ```(length > 0 && length < 256)``` | :heavy_check_mark: |
| ssn | Social Security Number | Numbers Only | ```length === 9``` | :heavy_check_mark: |
| phoneNumber | Phone Number | Numbers Only | ```(length > 6 && length < 16)```  | :heavy_check_mark: |
| driversLicense | Driver's License | none | ```(length > 5 && length < 17)``` | :heavy_check_mark: |
| email | Email Address | none | includes the characters '```@```' and '```.```' | :heavy_check_mark: |
| passportNumber | Passport Number | none | ```(length > 4 && length < 25)``` | :heavy_check_mark: |
| bankAccountNumber | Bank Account Number | none | ```(length > 0 && length < 41)``` | :heavy_check_mark: |  
  
  <br />

# ```teInputsMap``` Object Properties and Overrides
| Property Name | Description | Required |
|:--:|:--:|:--:|
| inputName | [```inputName```](#Input-Boxes-Available) desired to be displayed | :heavy_check_mark: |
| containerName | Name of container for token to be stored in  | :heavy_check_mark: |
| tokenName | Name of token for storage within provided container | |
| labelText | Custom text to override the input's label | :heavy_check_mark: (*required only for generic inputs) |
| colSpacingLg | Number of columns (out of 12) for input box on large screens (1280+ pixels) | |
| colSpacingMd | Number of columns (out of 12) for input box on medium screens (960 - 1279 pixels) | |
| colSpacingSm | Number of columns (out of 12) for input box on medium screens (<= 959 pixels) | |

<br />

2. The next step is to create a TokenExchangeConnect instance and feed that instance, with the ```'teInputsMap'```, to the wrapper around your application.   

```javascript
import React from 'react';
import { createTokenInstance } from '@magensa/token-exchange';
import { TokenExchange } from '@magensa/token-exchange-react';
import ExampleApp from './components/exampleApp';

const tokenExchangeInstance = createTokenInstance("__publicKeyGoesHere__");

const teConnectInputs = [
  {
    inputName: "firstName",
    containerName: "exampleContainerName"
  },
  {
    inputName: "middleName",
    containerName: "exampleContainerName"
  },
  {
    inputName: "lastName",
    containerName: "exampleContainerName"
  },
  {
    inputName: "ssn",
    containerName: "anotherContainerName"
  }
];

const App = () => (
    <TokenExchange 
        tokenInstance={ tokenExchangeInstance }
        teInputsMap={ teConnectInputs }
    >
        <ExampleApp />
    </TokenExchange>
);

export default App;
```  
  
- It's recommended to place this instance at the entrypoint of your application, to avoid multiple re-renders - or creating a new instance accidentally.
    

3. Next, once you have your form designed - drop the ```TokenInputs``` component in the place of your choosing.
    ```javascript
    import React from 'react';
    import { TokenInputs } from '@magensa/token-exchange-react';

    const appStyles = {
        height: '100%'
    }

    export default () => (
        <div styles={ appStyles }>
            <TokenInputs />
        </div>
    );
    ```

    - It's recommended to wrap the ```TokenInputs``` with a ```<div>``` of your choosing - so you may apply wrapper styles directly to your own div for juxtaposition. It's important to note that the ```height``` of this component is set to ```100%``` so that it may respond to your application's wrapper styles. As for styles applied to the component itself - we have a [styles object](#Styles-API) that you may use to inject your own custom styles.  
    - ```stylesConfig``` prop is optional. If provided, it will override the defaults.

 4. Finally, to submit the inputs and tokenize the data, utilize the ```createTokens``` function and attach to your click handler.  
 ```javascript
    import React from 'react';
    import { useTokenExchange, TokenInputs } from '@magensa/token-exchange-react';

    const customStyles = {
        base: {
            wrapper: { 
                margin: 0, 
                padding: 0
            },
            variants: {
                inputType: 'outlined',
                inputMargin: 'dense',
                autoMinHeight: true
            },
        backgroundColor: 'rgb(66, 66, 66)'
        },
        boxes: {
            labelColor: "#BB86FC",
            textColor: "#BB86FC",
            borderRadius: 10,
            errorColor: "#CF6679",
            inputColor: '#121212'
        }
    };

    export default () => {
        const createTokens = useTokenExchange();

        const clickHandler = async(e) => {
            try {
                const teConnectResponse = await createTokens();
                const { error } = teConnectResponse;

                if (error)
                    console.log("Unsuccessful, message reads: ", error);
                else
                    console.log('[TEConnect Result]:', teConnectResponse);
            }
            catch(err) {
                console.log('[Catch]:', err);
            }
        }

        return (
          <React.Fragment>
            <TokenInputs stylesConfig={ customStyles } />

            <button onClick={ clickHandler }>Submit</button>
        </React.Fragment>
        )
    }
 ```
- Make sure to ```await``` this call, since it is asyncronous, and additionally be sure to wrap the call in a ```try/catch```.
- It's important to note that while there are some cases which will throw an exception (```catch```) - there are other cases in which an object will return successfully with an error message. Make sure you check for an ```error``` property on the returned object. If it's not present - the call is succesful. For additional information, please see the possible [return objects](#createTokens-Return-Objects)

- Note that in the example above, we chose to provide customStyles to the ```TokenInputs``` component. ```stylesConfig``` is a optional prop. 

<br />

# Generic Inputs
TEConnect supports the use of generic input boxes. Unlike the [managed inputs](#Input-Boxes-Available-Managed-Inputs), these input boxes do not have any validation or input handling.  When building a map that includes generic input boxes, the process is slightly different from the [managed inputs](#Input-Boxes-Available-Managed-Inputs). First, to opt-in to a generic input - supply the string ```"_teconnect_generic"``` as the ```inputName``` property.  There are two additional required properties (```containerName``` and ```labelText```, respectively), but it is also recommended to provide a ```tokenName``` as well.  

With managed inputs, the ```inputName``` becomes the tokenName at creation. This is easy to manage as the ```"email"``` input box would become the ```"email"``` token.  With generic inputs, however, you may include as few or as many as you would like. It is therefore recommended to provide a ```tokenName``` of your choosing to assist in managing your created tokens. The ```tokenName``` is not required and will generate under the name ```"_teconnect_generic"```, if not provided.


```javascript
{
    inputName: "_teconnect_generic", // signals use of generic input
    containerName: "preferredContainerName", // required for all inputs
    labelText: "Label For Input", // required for generic input boxes
    tokenName: "example_generic_token_name" //not required, but recommended
}
```

# createTokens Return Objects
These are the possible objects that will be returned *successfully* from the ```createTokens``` function. Thrown errors will be thrown as any other async method.  
  
  1. ### Success:
```typescript
type TokenResponse = {
    code: String
    containerName: String
    customerTranRef: String
    magTranID: String
    message: String
    timestamp: String
    tokenKey: null | String
    tokenName: String
    tokenStatusCode: Number
}

type TokenResponses = {
    status: Number,
    tokenResponses: Array<TokenResponse>    
}
```  
  
  2. ### Bad Request
Bad Request is possible on two layers. The optional properties below demonstrate all possible properties of both objects, with the mandatory properties being shared amongst the two.

```typescript
type TokenResponse = {
    error: String,
    tokenName: String,
    tokenStatusCode: Number,
    code?: Number,
    containerName?: String,
    customerTranRef?: String,
    magTranID?: String,
    message?: String,
    timestamp?: String
}

type TokenResponses = {
    status: Number,
    tokenResponses: Array<TokenResponse>    
}
```

3. ### Error (Failed Validation, Timeout, Mixed Protocol, etc)
```typescript
type ClientError = { 
    error: String 
}
```

# Styles API
The styles object injected is composed of two main properties:
- ```base```  
    - General styles applied to the container.
- ```boxes```
    - Styles applied to the input elements.

Below we have the complete API with examples of default values for each.

### Base
| Property Name | Parent Property | Input Type | Acceptable Values | Default Value | Notes |
|:--:|:--:|:--:|:--:|:--:|:---:|
| backgroundColor | base | ```string``` | jss color (rgb, #, or color name) | ```"#fff"``` | container background color |
| margin | wrapper | ```string``` or ```number``` | jss spacing units (rem, em, px, etc) | ```'1rem'``` | container margin |
| padding | wrapper | ```string``` or ```number``` | jss spacing units (rem, em, px, etc) | ```'1rem'``` | container padding |
| inputType | variants | ```string``` | ```"outlined", "filled", "standard"``` | ```"outlined"``` | template design for input boxes |
| inputMargin | variants | ```string``` | ```"dense", "none", "normal"``` | ```"normal"``` | template padding & margins for input boxes |  
| autoMinHeight | variants | ```boolean``` | ```boolean``` | ```false``` | ```true``` will maintain a static margin on each input box that will not grow with validation errors |
  
<br />

### Default Base Object:
```javascript
{
    base: {
        wrapper: {
            margin: '1rem',
            padding: '1rem'
        },
        variants: {
            inputType: 'outlined',
            inputMargin: 'normal',
            autoMinHeight: false
        },
        backgroundColor: '#fff'
    }
}
```
<br />

### Boxes
| Property Name | Input Type | Acceptable Values | Default Value | Notes |
|:--:|:--:|:--:|:--:|:--:|
| labelColor | ```string``` | jss color (rgb, #, or color name) | ```"#3f51b5"``` | label text and input outline (or underline) color |
| textColor | ```string``` | jss color (rgb, #, or color name) | ```"rgba(0, 0, 0, 0.87)"``` | color of text for input value *Also applies :onHover color to outline/underline* |
| borderRadius | ```number``` | numerical unit for css ```border-radius``` property | 4 | border radius for input boxes |
| inputColor | ```string``` | jss color (rgb, #, or color name) | ```"#fff"``` | input box background color |  
| errorColor | ```string``` | jss color (rgb, #, or color name) | ```"#f44336"``` | Error text and box outline (or underline) color |  
  
<br />

### Default Boxes Object:
```javascript
{
    boxes: {
        labelColor: "#3f51b5",
        textColor: "rgba(0, 0, 0, 0.87)",
        borderRadius: 4,
        errorColor: "#f44336",
        inputColor: "#fff"
    }
}
```


# Example Implementation

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { TokenExchange, TokenInputs, useTokenExchange  } from '@magensa/token-exchange-react';
import { createTokenInstance } from '@magensa/token-exchange';

const teConnectInputs = [
    {
        inputName: "firstName",
        containerName: "exampleContainerName"
    },
    {
        inputName: "middleName",
        containerName: "exampleContainerName"
    },
    {
        inputName: "lastName",
        containerName: "exampleContainerName",
        tokenName: "custom_last_name_token_name",
        colSpacingLg: 3,
        colSpacingMd: 4,
        colSpacingSm: 5
    },
    {
        inputName: "ssn",
        containerName: "anotherContainerName"
    },
    {
        inputName: "_teconnect_generic",
        labelText: "Custom Text for Label",
        containerName: "exampleContainerName",
        tokenName: "custom_token_name"
    }
];

const customStyles =  {
    base: {
        wrapper: {
            margin: '2rem',
            padding: '2rem'
        },
        variants: {
            inputType: 'outlined',
            inputMargin: 'dense',
            autoMinHeight: false
        },
       backgroundColor: '#ff7961'
    },
    boxes: {
        labelColor: "#9400D3",
        textColor: "#90ee02",
        borderRadius: 10,
        errorColor: "#2196f3",
        inputColor: '#ff7961'
    }
}

const ExampleApp = () => {
    const createTokens = useTokenExchange();

    const clickHandler = async(e) => {
        try {
            const teConnectResponse = await createTokens();
            const { error } = teConnectResponse;

            if (error)
                console.log("Unsuccessful, message reads: ", error);
            else
                console.log('[TEConnect Result]:', teConnectResponse);
        }
        catch(err) {
            console.error(err);
        }
    }
    
    return (
        <React.Fragment>
            <TokenInputs stylesConfig={ customStyles } />
            
            <button onClick={ clickHandler }>Create Tokens</button>
        </React.Fragment>
    )
}

const tokenExchangeInstance = createTokenInstance("__publicKeyGoesHere__");

const App = () => (
    <TokenExchange 
        tokenInstance={ tokenExchangeInstance }
        teInputsMap={ teConnectInputs }
    >
        <ExampleApp />
    </TokenExchange>
);

ReactDOM.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>,
    document.getElementById('root')
);
```


## Minimum Requirements
There are three dependencies to use this product. If you built your project using [create-react-app](https://github.com/facebook/create-react-app), then you have already met the requirements. If you are building your own React app, then please make sure to include the following in your project:  

- [React](https://github.com/facebook/react) ^16.8
- [react-dom](https://github.com/facebook/react/tree/master/packages/react-dom) ^16.8
- [uuid](https://github.com/uuidjs/uuid) ^3.4.0
