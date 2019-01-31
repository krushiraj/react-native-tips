# react-native-tips
Tips for react-native

# tip 1: setting a default family across the application
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
