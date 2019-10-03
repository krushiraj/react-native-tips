# React-Native-Tips
Tips for react-native

# Setting a default family across the application
Sometimes we face issues in rendering text in application. I personally faced this issue while testing the app on OnePlus(with OnePlus Slate as font) and Oppo devices.
As all the elements in react are compatible and optimized for Roboto we can use it as default font family with the below hack.

<code>

	export default function enableFontPatch() {
		const React = require('react');
		const { Platform, Text } = require('react-native');
		const defaultFontFamily = {
			...Platform.select({
				android: { fontFamily: 'Roboto' }
			})
		};

		const oldRender = Text.render;
		Text.render = function(...args) {
			const origin = oldRender.call(this, ...args);
			return React.cloneElement(origin, {
				style: [defaultFontFamily, origin.props.style]
			});
		};
	}
	
</code>

call this function in your app constructor.


# Don’t bother with Expo
You should never use Expo on a serious project, for a number of reasons.
Firstly, the entire build process is not done locally, but in Expo cloud, which means little to no control and potential server issues while building. Secondly, you are adding another layer of complexity to your project, which slows it down and increases the app size.
Most importantly, native iOS/Android modules, which you are certain to need at one point, are not supported. The only native modules that are available to you are the ones provided by the Expo SDK.
Sooner or later, you will reach a point where you will have to eject the Expo app into a regular React Native app, so you might as well not waste your time and start the project right way anyway.

# Use ESLint
Using a linter on any project is a must, especially for a wild language such as JavaScript. ESLint is a fantastic extension which is extremely easy to install, set up and use, so be sure to include it in your project from the very beginning. If you disagree with some of the rules, you can always disable them in the .eslintrc configuration file, but having some kind of code quality control is going to help you and your team immensely in the long run.

# Remove all logs from your release builds
Having many console.log statements can slow your app down, especially if you are using logging libraries such as redux-logger. Be sure to disable all logs (manually or with a script) when making a release build.

# Use Flexbox
This should really be a no-brainer, but sadly I’ve seen React Native code that uses absolute element positioning instead of flexbox, which breaks the layout and provides no reusability whatsoever. No matter what your design requirements are, using flexbox is almost always the correct answer. For those who come from web background: Flexbox in React Native works almost exactly the same as its CSS counterpart, the important difference being that flexDirection defaults to column instead of row in React Native.

# Name your style objects consistently
If your root view has a style object named container, then use that name for every root view on the project. If your submit button has a style object named submitButton, then stick with it and don’t use saveButton, submitBtn or submit for other instances.

# Think about smart and dumb components
This is extremely important for any React Native project because classifying your components as smart or dumb will help you organize your project logically and also reuse your components better.
Smart components, also called class-based or container components are concerned with how things work. This means they hold the state, fetch data from the API, do most of the computing, keep track of the lifecycle methods if needed, provide data to other components, and have no styling included. Dumb components, also called functional or presentational components are concerned with how things look. They simply receive data (props) through parent components and display it to the user. They usually have no state and minimal logic, and contain all the styling. They are highly reusable because they provide a simple functionality based on the input data.

# Use ternary operators concisely
Writing color = error ? ‘red’: ‘gray’ is short and sweet.
Writing color = error ? (id === myID) ? ‘red’ : ‘black’ : ‘gray’ is not. Ternary operators can help reduce the number of lines in your code but never nest them because the logic becomes difficult to read.

# Don’t abuse zIndex
Use zIndex only when you have to. For example, if you want to overlay a <Text> over an <Image>, using zIndex is the wrong way to do it. You should use <ImageBackground> component instead. A good rule of thumb is that if you think you are over-complicating your code with a lot of zIndex properties, you are probably right. Go back to square one and rethink your layout.
	
# setState() is asynchronous
This has to be one of the most common beginner errors in React Native. Although changing the state of a component causes a re-render, if you write something like setState({ username: ‘somevalue’ }), and then try to read this.state.username in the next line of code, you will not be reading the correct value, because setState() is an asynchronous operation. Use await setState({ username }) to avoid this problem.

# You can dispatch redux actions from other actions
One action doesn’t have to be limited to only one API request, or one kind of data manipulation. Any action can be dispatched from another action, from the same or a different file. It sounds obvious but can be overlooked.

# Don’t leave sensitive data in your app
This includes API keys, API secrets, project IDs, client secrets, domains and any other data that is too sensitive to hold inside the app. Keep all of these on the server, not in your project.

# Manage your image resources properly
Use descriptive image names, because name login_button_disabled.png is far more clear and reuseable than login_button_gray.png. Always provide @1x, @2x, and @3x dimensions to be able to handle different screen densities on both iOS and Android. If the dimensions of your image assets are too small or not properly labeled, you might have blurred images in your app, which makes your app look very unprofessional. On the other hand, if you want to reduce file size of all your images easily without losing any quality, use applications such as ImageOptim.

# Use object destructuring
Nobody wants to see this.props.navigation.state.params.username all over the place. So use object destructuring. It makes code easier to read and understand. And you can (and should) also use it in function parameters, so instead of const MyComponent = (props) => { you can write const MyComponent = ({ username, userID }) => {.
You need to decide when to use destructuring and when not to, but most of the time it is a good idea. It’s not a good idea if it unnecessarily makes small functions a lot bigger, or if using it creates variable name conflicts. For example, if you have variables like this.props.id and this.state.id, destructuring would reduce both variables to the same name id, which is not good.

# DRY everywhere
If I had to name one programming principle that is a basis for everything else in software development, it would be the Don’t Repeat Yourself principle. Whenever you write the same piece of code twice, refactor it into something reusable. Even if it’s not completely the same but similar, it’s worth considering abstracting it. Create your own reusable components and utility methods. You’ll be glad you did later, as it will skyrocket your efficiency and make your project modular. If all your buttons look the same, with a specific font size, color and border radius, making a wrapper component is, of course, a good idea. But don’t stop there. Make wrapper components for anything you use repeatedly in your app. You have text buttons? Make a reusable component <TextButton> whose sole purpose is to wrap <TouchableOpacity> and <Text>. You have multiple image buttons? Make a component <ImageButton> that wraps <TouchableOpacity> and <Image>. The sky is the limit!
	
# On using external libraries
If there is a library out there that satisfies your needs and it isn’t abandoned, it’s better to use it instead of reinventing the wheel. You’ll save your precious development time for something else. However, you do need to do some research on the libraries you plan to use. Is it active and regularly maintained? Does it have a good rating? Is it well tested? Does it have a lot of issues? Does it support both iOS and Android? As a rule of thumb, if learning to use an external library is going to take more of your time than making everything yourself, you should stay away from it.
Sometimes you will want to change something in an external library. Do not edit it directly in the node_modules/ folder. That folder is supposed to be ignored by version control anyway, so if you change the code directly, your teammates will not see your changes. In addition, an npm update action will overwrite your modifications. The solution is to either fork the original repository and link your project to your own repository where you made the changes (and even make a PR to the original author if you want to help!), or if the library is very small (one file), you can copy/paste it as a component in your own project and then edit it locally.

# Use Live Reloading, Hot Reloading, and Inspector
Each one of these React Native features is extremely useful, so be sure to use all three. Live Reloading saves you time you would spend manually refreshing the app, Hot Reloading saves you time it takes to redesign a screen that is not easily accessible in your app, and Inspector saves you tons of console.log statements.

# Use async/await syntax, but not blindly
If you are a React Native developer, you are probably already using async/await syntax. It looks clean and it saves you from callback hell. However, beware that this syntax effectively makes your asynchronous code synchronous, so before you use it, ask yourself: Do you really need this? Is there something you need to do in parallel with your previous code? If so, you might need to rethink your approach. If you have several API calls that in no way depend on one another, the best thing you can do is create a promise for each of those async calls, put them all in an array, and run Promise.all() on it. This resulting “group” promise will finish as soon as all the promises inside are done, which is definitely much faster than running each request one after another.

# Modifying the contents of a state array/map will not cause a re-render
If you have a state variable that is a hashmap, modifying its contents won’t modify the variable itself, so render() will not be called. To avoid this, you need to make a deep copy of the variable, for example using _.cloneDeep() function from the lodash library.

# Style overriding
If you need to override an existing style object or modify the style dynamically, you can do that easily with this syntax:
<View style={[ containerStyle, myStyle ]}>...</View>,
where myStyle is the style object that overrides the containerStyle.

# Mind the iOS Safe Area
In order for your app to look good on all iOS devices, you need to wrap every screen of your app in a safe area view to avoid obstruction of views. Even better, you can make a wrapper component for React Native’s <SafeAreaView>, where you can add everything else you need to have on every screen of your app, such as OfflineNotice indicator for example.
	
# Don’t use TouchableWithoutFeedback unless you have a good reason
As the official React Native documentation states, all elements that respond to press should have a visual feedback when touched. Keep this in mind when creating your UI elements, and use <TouchableWithoutFeedback> sparingly.

# Thoroughly test new components for edge cases
If your new component has a username label, be sure to try it with an absurdly long username and see if it wraps nicely or goes off the layout. That’s also a good time to set up ellipsizeMode and numberOfLines for the <Text> component if needed. The same principle goes for any component. Torture it with different data and see how it behaves before finishing it, because debugging layout breaks will take a lot more time later.
	
# Use crash reporting mechanisms and log failed use cases
You should definitely have some kind of a crash reporting mechanism like Crashlytics. But besides that, it is very useful to log all failed use cases somewhere. You can use services such as Amplitude, your own backend or something else entirely. Do your users often enter their username instead of email on login screen? Or maybe they are reporting login errors in your app but in reality they don’t even know their own credentials? I’ve seen it all happen, everything is possible. Log as much as you can so you can save everyone's time, but also to see where your users often make mistakes and use that information to improve your app.

# Make API calls in componentDidMount()
It’s well-known that you should never, ever, fetch data in render() method, but componentWillMount() is not the right place to do it either. First of all, it is a legacy method, which alone should be a reason enough not to use it. Secondly, it will not prevent an additional render() call, so there aren’t any performance improvements either. The final reason is that you always need to handle default state in order to render the component with empty state values. So put all your API calls in componentDidMount() in order to always have the correct flow of data and rendering.

# Think about backwards compatibility
Sometimes a specific feature is removed, or some other breaking change is introduced like for example, a different response JSON on an API endpoint. Always be aware that some of your users won’t update the app to a new version right away, and their old version might crash or act in some other unexpected way.
