# 🔐 Webex React OAuth Sample

The purpose of this project is to show how to authorize with Webex APIs within a React application. The setup is a basic site that utilizes the implicit grant flow available for [Webex Integrations](https://developer.webex.com/docs/integrations), providing a clean foundation for building React applications that need Webex authentication.

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## ✨ Features

- **🔒 OAuth Implicit Grant Flow** - Secure client-side authentication with Webex
- **⚛️ React Hooks Integration** - Modern React patterns with custom hooks
- **🎯 Token Management** - Automatic token extraction and URL cleanup
- **🔗 Dynamic Redirect URI** - Automatic redirect URI generation for any deployment
- **⚡ Environment Configuration** - Easy setup with environment variables
- **🚀 Live Demo** - Hosted GitHub Pages deployment for immediate testing
- **📱 Responsive Design** - Works across desktop and mobile devices

## 🌐 Live Demo

View the live demo here: **https://webexsamples.github.io/webex-react-oauth-sample/**

## 🚀 Project Setup

The project uses environment variables to configure the Webex API Integration.

### Prerequisites

- Node.js 14.x or later
- Yarn or npm package manager
- Valid Webex Integration from [Webex Developer Portal](https://developer.webex.com/my-apps)

### Installation & Configuration

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd webex-react-oauth-sample
   ```

2. **Install dependencies:**
   ```bash
   yarn install
   # or
   npm install
   ```

3. **Configure environment variables:**

   #### Usage with `.env`
   
   Project configuration lives in a `.env` file. A sample `.env.sample` has been provided for ease of use.
   
   - Rename the `.env.sample` file to `.env`
   - Fill in your client ID provided from the [Webex Developer Portal](https://developer.webex.com/my-apps)
   
   ```bash
   # Copy the sample file
   cp .env.sample .env
   
   # Edit the .env file with your credentials
   REACT_APP_WEBEX_CLIENT_ID=your_client_id_here
   REACT_APP_WEBEX_BASE_URL=https://webexapis.com/v1/authorize
   ```

4. **Start the development server:**
   ```bash
   yarn start
   ```

5. **Access the application:**
   - Open [http://localhost:3000](http://localhost:3000) to view it in the browser
   - Click "Login to Webex" to initiate OAuth flow
   - Complete authentication and return to see token confirmation

## 📖 Usage Guide

### OAuth Flow Process

1. **Initial Load:**
   - Application checks for existing OAuth token in URL hash
   - If no token exists, displays OAuth login link

2. **Authentication:**
   - User clicks "Login to Webex" link
   - Redirects to Webex OAuth authorization page
   - User grants permissions to the application

3. **Token Handling:**
   - Webex redirects back with access token in URL hash
   - Custom hook extracts token and stores in React state
   - URL hash is cleaned for security

4. **Authenticated State:**
   - Application displays success message
   - Access token is available for API calls via `webexToken` variable

### Integration Example

```javascript
// Basic usage in your React component
function App() {
  const webexToken = useWebexOAuth();
  const redirectURI = useCurrentUri();

  return (
    <div>
      {webexToken ? (
        <div>
          Welcome! Your token: {webexToken}
          {/* Use token for Webex API calls */}
        </div>
      ) : (
        <OAuthLink 
          clientID={process.env.REACT_APP_WEBEX_CLIENT_ID}
          loginText="Login to Webex"
          redirectURI={redirectURI}
          webexAPIBaseURL={process.env.REACT_APP_WEBEX_BASE_URL}
        />
      )}
    </div>
  );
}
```

## 🏗️ Project Structure

```
webex-react-oauth-sample/
├── public/
│   ├── index.html              # Main HTML template
│   ├── favicon.ico             # Site favicon
│   ├── manifest.json           # PWA manifest
│   └── robots.txt             # Search engine directives
├── src/
│   ├── App.js                 # Main application component
│   ├── App.css                # Application styles
│   ├── OAuthLink.js           # OAuth authorization link component
│   ├── useWebexOAuth.js       # OAuth token management hook
│   ├── useCurrentUri.js       # Current URL handling hook
│   └── index.js               # React application entry point
├── .env.sample                # Environment variables template
├── package.json               # Dependencies and scripts
├── LICENSE                    # Cisco Sample Code License
└── README.md                  # This documentation
```

### Core Components

| Component | Description | File Location |
|-----------|-------------|---------------|
| **App** | Main application container and OAuth routing | [`App.js`](src/App.js) |
| **OAuthLink** | Webex OAuth authorization link generator | [`OAuthLink.js`](src/OAuthLink.js) |
| **useWebexOAuth** | OAuth token extraction and management hook | [`useWebexOAuth.js`](src/useWebexOAuth.js) |
| **useCurrentUri** | Dynamic redirect URI generation hook | [`useCurrentUri.js`](src/useCurrentUri.js) |

## 🔧 React Hooks Implementation

### OAuth Token Hook

```javascript
// useWebexOAuth.js - Handles token extraction from URL hash
function useWebexOAuth() {
  const [webexToken, setWebexToken] = useState('');
  const { hash } = window.location;

  useEffect(() => {
    if (!webexToken && hash) {
      const parsedHash = queryString.parse(hash);
      // Extract access token from hash
      parsedHash['access_token'] && setWebexToken(parsedHash['access_token']);
      
      // Clean sensitive data from URL for security
      ['access_token', 'token_type', 'expires_in', 'refresh_token', 'refresh_token_expires_in']
        .forEach((key) => Reflect.deleteProperty(parsedHash, key));
      window.location.hash = queryString.stringify(parsedHash);
    }
  }, [webexToken, hash]);

  return webexToken;
}
```

### Current URI Hook

```javascript
// useCurrentUri.js - Generates proper redirect URI
function useCurrentUri() {
  const [currentUri, setCurrentUri] = useState('');

  useEffect(() => {
    let redirect_uri = `${window.location.protocol}//${window.location.host}`;
    
    if (window.location.pathname) {
      redirect_uri += window.location.pathname;
    }
    
    // Remove trailing slash (Webex OAuth requirement)
    redirect_uri = redirect_uri.endsWith('/') ? redirect_uri.slice(0, -1) : redirect_uri;
    
    setCurrentUri(encodeURIComponent(redirect_uri));
  }, []);

  return currentUri;
}
```

### OAuth Link Component

```javascript
// OAuthLink.js - Generates authorization URL
function OAuthLink({clientID, loginText, redirectURI, webexAPIBaseURL}) {
  return (
    <a href={`${webexAPIBaseURL}?client_id=${clientID}&response_type=token&redirect_uri=${redirectURI}&scope=spark%3Aall%20spark%3Akms`}>
      {loginText}
    </a>
  );
}
```

## ⚙️ Configuration

### Environment Variables

```bash
# Required OAuth settings
REACT_APP_WEBEX_CLIENT_ID=your_webex_client_id
REACT_APP_WEBEX_BASE_URL=https://webexapis.com/v1/authorize
```

### OAuth Scopes

The application requests the following OAuth scopes:
- `spark:all` - Full access to Webex APIs
- `spark:kms` - Key Management Service access for secure operations

### Webex Integration Setup

1. **Create Integration:**
   - Visit [Webex Developer Portal](https://developer.webex.com/my-apps)
   - Click "Create a New App" → "Integration"
   - Configure redirect URI based on your deployment

2. **Redirect URI Configuration:**
   - Development: `http://localhost:3000`
   - Production: Your deployed domain (e.g., GitHub Pages URL)

3. **Copy Client ID:**
   - Use the Client ID in your `.env` file
   - Client Secret is not needed for implicit grant flow

## 📜 Available Scripts

In the project directory, you can run:

### `yarn start`

Runs the app in the development mode.
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.
You will also see any lint errors in the console.

### `yarn build`

Builds the app for production to the `build` folder.
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `yarn deploy`

Deploys the built application to GitHub Pages.
This is configured with the `predeploy` script that automatically builds before deployment.

### `yarn eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## 🎯 Integration Examples

### Using the Token for API Calls

```javascript
// Example: Get user information
async function getUserInfo(webexToken) {
  const response = await fetch('https://webexapis.com/v1/people/me', {
    headers: {
      'Authorization': `Bearer ${webexToken}`,
      'Content-Type': 'application/json'
    }
  });
  
  return response.json();
}

// Usage in component
function UserProfile() {
  const webexToken = useWebexOAuth();
  const [userInfo, setUserInfo] = useState(null);
  
  useEffect(() => {
    if (webexToken) {
      getUserInfo(webexToken).then(setUserInfo);
    }
  }, [webexToken]);
  
  return userInfo ? <div>Welcome, {userInfo.displayName}!</div> : null;
}
```

### Building on This Sample

```javascript
// Extend the App component for your needs
function App() {
  const webexToken = useWebexOAuth();
  const redirectURI = useCurrentUri();

  if (webexToken) {
    // User is authenticated - show your main application
    return <MainApplication token={webexToken} />;
  }

  // User needs to authenticate
  return (
    <AuthenticationScreen 
      clientID={process.env.REACT_APP_WEBEX_CLIENT_ID}
      redirectURI={redirectURI}
    />
  );
}
```

## 🧪 Testing

### Manual Testing

1. **OAuth Flow Testing:**
   - Start application and verify OAuth link appears
   - Click login link and complete Webex authentication
   - Verify token is extracted and success message appears
   - Check that URL hash is cleaned after token extraction

2. **Environment Testing:**
   - Test with different client IDs
   - Verify redirect URI handling for different deployments
   - Test with various OAuth scopes

3. **Browser Compatibility:**
   - Test across different browsers
   - Verify mobile responsiveness
   - Check token handling consistency

### Browser Compatibility

| Browser | Version | OAuth Support |
|---------|---------|---------------|
| Chrome | 90+ | ✅ Full Support |
| Firefox | 88+ | ✅ Full Support |
| Safari | 14+ | ✅ Full Support |
| Edge | 90+ | ✅ Full Support |

## 🚨 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| **Redirect URI Mismatch** | Ensure redirect URI in integration matches current URL |
| **Client ID Not Found** | Verify REACT_APP_WEBEX_CLIENT_ID is set correctly |
| **Token Not Extracted** | Check browser console for hash parsing errors |
| **CORS Errors** | Ensure you're using the correct OAuth endpoints |

### Debug Tips

```javascript
// Add debugging to useWebexOAuth hook
useEffect(() => {
  console.log('Current hash:', hash);
  console.log('Parsed hash:', queryString.parse(hash));
  console.log('Extracted token:', webexToken);
}, [hash, webexToken]);
```

### Environment Variable Issues

```bash
# Verify environment variables are loaded
echo $REACT_APP_WEBEX_CLIENT_ID

# Check .env file format (no spaces around =)
REACT_APP_WEBEX_CLIENT_ID=your_client_id_here
```

## 🤝 Contributing

We truly appreciate your contribution to the Webex Samples!

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/oauth-enhancement`
3. Commit changes: `git commit -am 'Add OAuth feature'`
4. Push to branch: `git push origin feature/oauth-enhancement`
5. Submit a Pull Request

### Development Guidelines

- Follow React functional component patterns
- Use modern React hooks for state management
- Implement proper PropTypes validation
- Maintain OAuth security best practices
- Test across multiple browsers and devices

## 📚 Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Additional Resources

- [Webex OAuth Guide](https://developer.webex.com/docs/oauth)
- [Webex Integration Documentation](https://developer.webex.com/docs/integrations)
- [OAuth 2.0 Implicit Grant Flow](https://developer.webex.com/docs/oauth-implicit-grant-flow)
- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)

## 📄 License

This project is licensed under the Cisco Sample Code License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

For technical support and questions:

- **Issues**: Submit via GitHub Issues
- **OAuth Documentation**: [Webex OAuth Guide](https://developer.webex.com/docs/oauth)
- **Integration Support**: [Webex Developer Portal](https://developer.webex.com)
- **Community**: [Webex Developer Community](https://developer.webex.com/community)

## Thanks!

Made with ❤️ by the Webex Developer Relations Team at Cisco

---

**Note**: This sample demonstrates OAuth implicit grant flow for client-side applications. For production use, consider security best practices and token management strategies appropriate for your application architecture.
