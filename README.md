# React Scroll Utilities (Beta)
  ![npm](https://img.shields.io/npm/v/react-scroll-utilities?color=%2301b37f)
  ![npm bundle size](https://img.shields.io/bundlephobia/min/react-scroll-utilities)
  ![npm](https://img.shields.io/npm/dw/react-scroll-utilities?color=%23620d7e)
  ![GitHub closed issues](https://img.shields.io/github/issues-closed/zJaaal/react-scroll-utilities?color=%23895ee4)

React Scroll Utilities is a Lightweight library to track scroll events like, proximity to components, direction of scroll and render a component if it's on screen sight. I'll be adding more features and improving the functionality. I'm also open to read any request or change that you think that would be a good addition.

[Want to see a demo?](https://react-scroll-utilities.netlify.app)

## Disclaimer

This library is still on Beta phase, some components could get deprecated, implementations can change and generate breaking changes, so until futher notice I don't recommend to use it on real projects. Anyways I would be grateful if you use it and experiment with it. I'm still figuring out how to make some things and optimize implementations. So, if you have any ideas of new implementations/hooks/components please don't hesitate to open an issue. 

## Installation

You will need React ++16.8 to make this library work, since it use hooks as it's main construction.

You only need to do:  
``` 
npm i react-scroll-utilities 
or 
yarn add react-scroll-utilities
```

## ScrollWatcher Component

This component works as a context to read the scroll event.

### Usage

You only need to render this component on the top level of your app, it provides the necessary information for the library to work.

### Example

```js
// App.jsx / Main.jsx or App.tsx / Main.tsx

<ScrollWatcher>
  <YourRouterLib>
    <YourThemeProvider>
      <YourAppEntryPoint />
    </YourThemeProvider>
  </YourRouterLib>
</ScrollWatcher>
```

## useProximity hook

This hook lets you know how far is the screen from the component, it returns an object with 3 properties: "x", "y" and "onSight". "x" and "y", represents the proximity of the component from the center of the screen, it works as a Cartesian Plane, where up and right are positive values for "y" and "x", and down and left are negative values for "y" and "x", the values goes from -innerHeight/2 to +innerHeight/2 or -height/2 to +height/2 in case that the component is bigger than the viewport, that is for "y" value, for "x" is the same but using the width of the viewport or the width of the component. onSight is a boolean that tells you if the component is inside the viewport or not.

So if your component is 2000h x 1000w it will go from -1000 to 1000 for Y axis, and from -500 to 500 for X axis. In case that your viewport is bigger than your component lets say 3000h x 2000w (hypothetical values). Proximity values will go from -1500 to 1500 for Y axis and from -1000 to 1000 for X axis. In both cases, 0 for X or 0 for Y is exactly the center of the component in the center of the viewport.

### Usage

This hook only take one argument that should be a ref to an HTML Element. This hook won't work if ScrollWatcher is not implemented.

#### Example

```js
//TypeScript
function Example() {
  const ref = useRef<HTMLDivElement>(null);

  //I'm destructuring the object but you can easily use it without destructuring
  const { x, y } = useProximity(ref);

  return (
    <div ref={ref}>
      <div>{"X proximity: " + x}</div>
      <div>{"Y proximity: " + y}</div>
    </div>
  );
}

//JavaScript
function Example() {
  const ref = useRef(null);

  //I'm destructuring the object but you can easily use it without destructuring
  const { x, y } = useProximity(ref);

  return (
    <div ref={ref}>
      <div>{"X proximity: " + x}</div>
      <div>{"Y proximity: " + y}</div>
    </div>
  );
}

// Since the implementation of Render got this simple, I just use it as an example
// Basically, with this property you can add classNames for entry animations or exit animations.
// Of course to determinate entry or exit, you could use useDirection 

//TypeScript
function Render:FC<RenderProps>({ children }) {
  const ref = useRef<HTMLDivElement>(null);

  //I'm destructuring the object but you can easily use it without destructuring
  const { onSight } = useProximity(ref);

  return (
    <div ref={ref}>
      //if this component is not on sight, its children won't render
      {onSight && children}
    </div>
  );
}

//JavaScript
function Render({ children }) {
  const ref = useRef(null);

  //I'm destructuring the object but you can easily use it without destructuring
  const { onSight } = useProximity(ref);

  return (
    <div ref={ref}>
      //if this component is not on sight, its children won't render
      {onSight && children}
    </div>
  );
}
```

## useDynamicColor Hook

This hook returns an RGB color that changes with the scroll, using an startColor, an endColor and a HTML Reference to calculate the current color on scroll.

### Usage

This hook takes an object with 3 properties, "startColor" and "endColor" that are arrays of number with a length of 3. Each value in the array is a color representing RGB, so each value should be between 0 and 255. That means that each array should be like: [123, 2, 215] or [0, 0, 0] or [255, 255, 255 ]. The third property is "elementRef" that is an HTML Reference that you can get using the useRef hook. 

Also it accepts an options object, to modify the behavior of the hook, options for now has just one property: anchor

anchor property is a string that can only be: "top", "middle" and "bottom". Its default value is "middle".

This represents which edge of the component will use the hook to calculate the current color. My recomendations are that you test it by yourself to see how it works. But I can tell you that if you have a component that is at the very top of your page, use anchor: "top" and if you have a component at the very bottom of your page, something like a footer, use anchor: "bottom".

It returns and string as: ```"rgb(123, 0, 43)"```

### Example

```js

//Here I created an example of a DynamicBackground
const DynamicBackground = ({
  startColor = [0, 0, 0],
  endColor = [255, 255, 255],
}) => {
  const elementRef = useRef(null);

  //color is a CSS valid color, it returns an string like: "rgb(123, 43, 67)"
  const color = useDynamicColor({ startColor, endColor, elementRef });

  //You can use it like this too
  const colorObj = {
    startColor: [123, 0, 24], //The color will vary from here
    endColor: [45, 34, 12], // To here
    elementRef: elementRef, //Using the height and the position of this element

    // Neither options or anchor are needed in this case because it will always have middle as default value, I just use it for example purposes
    options: {
      anchor: "middle" // Because this will cover all the app, since is a background
    }
  }

  const colorWithExternalObject = useDynamicColor(colorObj);

  return (
    <div
      ref={elementRef}
      style={{
        //So you just use the color wherever you want, background, font color, icon color, etc.
        backgroundColor: color,
      }}
    >
      {children}
    </div>
  );
};
```

## useLinearValue Hook

This hook returns a value that changes on scroll, using an startValue and an endValue to have a range of numbers, and a HTML Reference to calculate the current value on scroll,

### Usage

This hook takes an object with 3 properties, "startValue" and "endValue" that are numbers. The third property is "elementRef" that is an HTML Reference that you can get using the useRef hook. 

Also it accepts an options object, to modify the behavior of the hook, options for now has just one property: anchor

anchor property is a string that can only be: "top", "middle" and "bottom". Its default value is "middle".

This represents which edge of the component will use the hook to calculate the current color. My recomendations are that you test it by yourself to see how it works. But I can tell you that if you have a component that is at the very top of your page, use anchor: "top" and if you have a component at the very bottom of your page, something like a footer, use anchor: "bottom".

It returns the current value as number.

### Example

```js

//Here I created an example of an Spinning Square
const SpinningSquare = () => {
  const ref = useRef(null);

  //In this case we calculate degrees, so we go from 0 to 360
  const deg = useLinearValue({
    startValue: 0,
    endValue: 360,
    elementRef: ref,
  });

  const exampleDegObject = {
    startValue: 0,
    endValue: 360,
    elementRef: ref,
    options: {
      //In this case lets say that we are going to use the SpinningSquare in a footer, so we use anchor: "bottom"
      //Anyways, I recommend to test each of the possible anchor values to see how it behaves.
      anchor: "bottom",
    },
  };

  //Just an example of using an external object
  const exampleDeg = useLinearValue(exampleDegObject);

  return (
    <div
      style={{
        height: "100px",
        width: "100px",
        // Here we use the calculated degrees
        transform: `rotate(${deg}deg)`,
        borderRadius: "10px",
      }}
      ref={ref}
    >
      I'm a square that spins on scroll!
    </div>
  );
};

```

## useDirection hook

This hooks returns the current direction of the scroll. It returns an string that can be "UP", "DOWN", "RIGHT" or "LEFT", its default value is "DOWN".

### Usage

The only requirement in order to use it, is to make the right implementation of ScrollWatcher. It doesn't take any arguments at the time. You can use this hook in combination of useProximity to make different animations using the direction of the scroll as reference. You can go to src/test-components/BackgroundChange.tsx to see and example of a background that reacts to the scroll and direction.

### Example

```js
function Example() {
  const direction = useDirection();

  useEffect(() => {
    switch (direction) {
      case "UP": {
        // Do something
        break;
      }
      case "DOWN": {
        //Do something else
        break;
      }
      case "RIGHT": {
        // Do something
        break;
      }
      case "LEFT": {
        //Do something else
        break;
      }
    }
  }, [direction]);

  return (
    <div>
      {/*
      // Some JSX
      */}
    </div>
  );
}
```

## Directions enum

This lib provides an enum for TypeScript users, it just has four properties at the moment: Directions.up, Directions.down, Directions.right and Directions.left that returns the value of "UP", "DOWN" "RIGHT" and "LEFT", you can use it as helper for useDirection hook.

# Animations Components

## Circle Component

This component is a Circle that draws itself in response of the scroll event, it will start to draw when its on sight, any other way it will stand still at it's initial state.

## Usage

Just be sure that ScrollWatcher is at the top level of your app, any other way this component will throw an error.

## Props


| prop            | usage                                                                                                                                                                       | type         | default value | example values                                          | exceptions                                                                                                  |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|---------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| backgroundColor | Color of the background to match  the color of the container                                                                                                                | string       | "white"       | "#F323F1", "violet", "skyblue", "rgb(255, 153, 213)"... | No exceptions, if you pass something else it won´t work,  since it won't be a valid color                   |
| color           | Line color                                                                                                                                                                  | string       | "red"         | "#F323F1", "violet", "skyblue", "rgb(255, 153, 213)"... | No exceptions, if you pass something else it won't work,  since it won't be a valid color                   |
| clockwise       | Direction of the draw                                                                                                                                                       | boolean      | true          | true, false                                             | No exceptions, if you pass something else it will work with the  truthy/falsy JS logic                      |
| stroke          | Width of the line in pixels                                                                                                                                                 | number       | 2             | 1, 24, 3...                                             | stroke should be greater than 0, in other case it will throw an exception                                   |
| radius          | Radius of the circle                                                                                                                                                        | string       | "200px"       | "100px", "50vh", "21em"...                              | radius should have a valid measurement unit. Accepts any measurement unit but %, due to position issues     |
| children        | Children component to render  inside the circle                                                                                                                             | ReactElement | undefined     | ```<SomeComponent/> or <div>Hello world</div>```                                 | If you use something else as children, expect an error from React.                                          |
| startDegree     | Initial state of the line in degrees,  it can start with some portion of  the circle already drawed                                                                         | number       | 0             | 90, 180, 270, 45, 60, 75, 21.2, 3...                    | startDegree should be less than endDegree, in other case it will  throw an exception                        |
| endDegree       | Final state of the line in degrees,  to prevent the line from drawing  at some point of the circle                                                                          | number       | 360           | 90, 283, 180, 213, 270, 34.2                            | endDegree should be greater than startDegree, in other case it will throw an exception                      |
| rotate          | Initial state of the line in degrees, you can  set the start of the line at 90° instead  of 0°. This won't draw the circle,  it will move the starting position of the line | number       | 0             | 78, 90, 100, 80, 170, 45, 21.3, 56...                   | if you pass something else that's not a number, it won't work  since it's not a valid degree (at the time). |


## Example

This will render a circle that has a radius of 400 pixels and a line width of 4 pixels.

```js
//Some JSX...

<Circle radius="400px" stroke={4} />

//The rest of your JSX...
```

## Rectangle Component

This component is a Rectangle that draws itself in response of the scroll event, it will start to draw when its on sight, any other way it will stand still at it's initial state.

## Usage

Just be sure that ScrollWatcher is at the top level of your app, any other way this component will throw an error.

## Props

| prop            | usage                                                                                                                                                                          | type         | default value | example values                                          | exceptions                                                                                                  |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|---------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| backgroundColor | Color of the background to match  the color of the container                                                                                                                   | string       | "white"       | "#F323F1", "violet", "skyblue", "rgb(255, 153, 213)"... | No exceptions, if you pass something else it won´t work,  since it won't be a valid color                   |
| color           | Line color                                                                                                                                                                     | string       | "red"         | "#F323F1", "violet", "skyblue", "rgb(255, 153, 213)"... | No exceptions, if you pass something else it won't work,  since it won't be a valid color                   |
| clockwise       | Direction of the draw                                                                                                                                                          | boolean      | true          | true, false                                             | No exceptions, if you pass something else it will work with the  truthy/falsy JS logic                      |
| stroke          | Width of the line in pixels                                                                                                                                                    | number       | 2             | 1, 24, 3...                                             | stroke should be greater than 0, in other case it will throw an exception                                   |
| heigth          | Height of the rectangle                                                                                                                                                        | string       | "200px"       | "100vh", "50px", "21em"...                              | height should have a valid measurement unit. Accepts any measurement unit but %, due to position issues     |
| width           | Width of the rectangle                                                                                                                                                         | string       | "200px"       | "100vh", "1240em", "50vw"...                            | width should have a valid measurement unit. Accepts any measurement unit but %, due to position issues      |
| children        | Children component to render  inside the rectangle                                                                                                                             | ReactElement | undefined     | ```<SomeComponent/> or <div>Hello world</div>```                                  | If you use something else as children, expect an error from React.                                          |
| startDegree     | Initial state of the line in degrees,  it can start with some portion of  the rectangle already drawed                                                                         | number       | 0             | 90, 180, 270, 45, 60, 75, 21.2, 3...                    | startDegree should be less than endDegree, in other case it will  throw an exception                        |
| endDegree       | Final state of the line in degrees,  to prevent the line from drawing  at some point of the rectangle                                                                          | number       | 360           | 90, 283, 180, 213, 270, 34.2                            | endDegree should be greater than startDegree, in other case it will throw an exception                      |
| rotate          | Initial state of the line in degrees, you can  set the start of the line at 90° instead  of 0°. This won't draw the rectangle,  it will move the starting position of the line | number       | 0             | 78, 90, 100, 80, 170, 45, 21.3, 56...                   | if you pass something else that's not a number, it won't work  since it's not a valid degree (at the time). |

## Example

This will render a rectangle that has a height of 400px, a width of 100px and a line width of 5

```js
//Some JSX...

<Rectangle height="400px" width="100px" stroke={5} />

//The rest of your JSX...
```


## Recomendations

To make responsive the components, I recommend to use rem or em values, so that way you can modify the font-size of the root or parent component using media queries and therefore modify the sizes of the components.

For more information of how rem and em values work. [Just click here](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units) and go to the section of relative length units.