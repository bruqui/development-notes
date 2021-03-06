# Styling
A great way for styling react components

## CODING STANDARDS

All items in a component should have its own className. For instance, button.js component would have the parent class of .button. If there are elements that need to be styled within that component, you would add a `__semantic-name` to it such as `button__icon` if you had an icon in the button component. A lot of times that `__name` would be the name of the child component if it's another component. If it's something like an `<img />`, `<p />`, `<div />` you would just give it a semantic name such as `table__row` or `detail__item`. If the component needs to be modified in styling in some what such as a button needing a different background color, add a modifier type of class name using double dashes... `--modifier-name` such as `button--secondary`. More info on how to handle that below.

If the component has a rather large name, you can use a shorter name that is still descriptive to the component so you don't have to have super long names for elements within that component. For instance, if you had a component name `services-search-gallery-details`, you could do something like this...

html

```
<div className="services-search-gallery-details">
    <Icon className="gallery-details--icon" />
</div>
```

scss

```
.services-search-gallery-details {
    //styling goes here

    .gallery-details {

            &__icon {
                //styling goes here
            }
    }
}
```

Each component should have its own file for styling... that is if any styling is needed. For example button.js would have a button.scss file and any styling related to the button component will be placed in there. Inside the button.js file you will need to add the following code (generally at the bottom of all the other imports)...

```
// Styling
import './button.scss';
```

Here's the typical way of styling for that button.

```
.button {
  padding: 5px 7px;
  position: relative;

  &__icon {
      position: absolute;
      right: 3px;
  }
}
```

This will output two different parent classes, `.button` and `.button__icon`.

In this particular scenario, I've chosen to have a button with an icon. The JSX would looks something like this...

```
<button className="button"><Icon className="button__icon" /></button>
```
The purpose of this is to keep the styling scoped to just that component. It is also best to leave any type of layout styling out of the child type of components and let the parent component handle the layout styles. This way you have flexibility to do what you need with that component without having to do overrides and removes the need of using `!important`. Never style an element directly unless you have a case where it's absolutely necessary. If so, use the styles.scss file or possibly a `*-overides.scss` file if it's a library (node module) component with styling that needs to be overridden. For example, if you use Material UI, Foundation or Twitter Bootstrap.

That's just a simplified version of how the typical styling would go. If you wanted to have additional styling for this component that would be a unique circumstance in a parent component, you would need to add a way to pass a `className` prop to the button.js file and use the `classnames` library (`npm install classnames --save`) to set up all the classes associated with that core component. This is how all of the core components should be built. For example, as you can see above, there's a class being passed into the `Icon` component. Here's how that would look.

```
// Vendor Libs
import React, {Component} from 'redux';
import classnames from 'classnames';

// Styles
import './icon.scss';

export default class Icon extends Component {
  getClass() {
    return classnames({
        icon: true
    }, this.props.className)
  }

  render() {
    return (
      <i className={this.getClass()} />  
    );
  }
}
```
This would render...
```
<button class="button"><i class="icon button__icon"></i></button>
```
...when the `Button` component is its parent as illustrated in the button.js code above.

Now of course you wouldn't want to have the button always render an icon so you would need to set up props to control that by the Button component to show and not show that icon. The same would go for having different styles for the Button component. For instance, you could have a primary, secondary and tertiary button or maybe even a button that looks like a link. In these cases you want to make modifier classes for that button and use props to have them added like so...

```
  getClass() {
    return classnames({
      button: true,
      button--link: (this.props.type === 'link');
      button--secondary: (this.props.secondary),
      button--tertiary: (this.props.tertiary),
    });
  }
```
So `<Button type="link" />` would return `<button class="button button--link"></button>` and `<Button secondary />` would return `<button class="button button--secondary"></button>`. The styling would look something like...
```
.button {
  background-color: #900;

  &--secondary {
    background-color: #090;
  }

  &--tertiary {
    background-color: #009;
  }

  &--link {
    background-color: transparent;
    border: 0;
    margin: 0;
    padding: 0;

    &:hover {
      text-decoration: underline;
    }
  }
}
```

Let's say you had a button that was normally a light color, but you also need a dark button and inside that button you have an icon. When the button is light, you want the icon to be dark and when the button is dark you want the icon to be light. Here's how you would handle that.

The styling would look something like this...

```
.button {
    background-color: #FFF;

    &__icon {
        color: #333;
    }

    &--dark {
        background-color: #333;

        .button {

            &__icon {
                color: #FFF;
            }
        }
    }
}
```

The javascript would look like this...

```
// Vendor Libs
import React, {Component} from 'redux';
import classnames from 'classnames';

// Components
import Icon from './icon.js';

// Styles
import './button.scss';

export default class Button extends Component {
    getClass() {
        classnames({
            button: true,
            button--dark: this.props.dark
        })
    }

    render() {
        return (
            <button className={this.getClass()}>
                <Icon icon="someIcon" className="button__icon" />
            </button>
        )
    }
}
```

The scss may look a little unnatural, but what this does is makes it so you don't have to make special classes for any items within a component such as `__icon` in this case. So when the button is in it's normal light state, the css affecting the component would simply be this...

html

```
<button class="button">
    <i class="icon icon_some-icon button__icon"></i>
</button>
```

css affecting this

```
.button {
    background-color: #FFF;
}

.button__icon {
    color: #333;
}
```

If you set the dark prop to true, it would come out like this...

html

```
<button class="button button--dark">
    <i class="icon icon--some-icon button__icon"></i>
</button>
```

css affecting this

```
.button--dark {
    background-color: #333;
}

.button--dark .button__icon {
    color: #FFF;
}
```


When naming the styles, try to keep them generic, but semantic. Don't name them in a way that has to do with a specific color. Think theming. So button_green wouldn't be very helpful if you wanted it to be a different color for a different theme. Also, keep all the colors you're going to use in the `_colors.scss` file and set them as a variable that makes sense. Again stay away from using colors for the name.

The above code also should give you a good idea of keeping the core components as generic as possible. Only make changes to these in cases where you know that functionality or styling is going to be used globally in the app. Otherwise, keep that functionality in the parent component and use callback functions or props to set different modifier styles to handle that functionality. For instance, the Button component should really only need an onClick prop... (maybe a mouseOver and mouseOut). So make the onClick a prop that can be passed into the component and pass a function from the parent component that handles that onClick.
