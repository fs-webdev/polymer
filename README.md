FamilySearch Polymer Style guide
==========

*A mostly reasonable approach to Polymer and Web Components*

## Table of Contents

  1. [General](#general)
  1. [Naming](#naming)
  1. [Attributes](#attributes)
  1. [Events](#events)
  1. [Properties](#properties)
  1. [Methods](#methods)
  1. [Bindings](#bindings)
  1. [Documentation](#documentation)
  1. [Accessibility](#accessibility)
  1. [Resources](#resources)

## General

  - Web Component APIs and attribute names should be consistent with native DOM elements.
  - Follow the single-responsibility principle.
  - Extend existing elements (e.g. [PolymerElements](https://elements.polymer-project.org/)) instead of reimplmenting the functionality.
  - Fail silenty. Components should act like native DOM and should not throw JS errors. Fire an [event](#events) instead.
  - List all dependencies of the web component since the browser will dedup mulitple requests for the same file.

## Naming

  - Component names must include a dash. The text before the dash is effectively a namespace.
  
  ```html
  <!-- bad -->
  <myComponent></myComponent>
  
  <!-- good -->
  <my-component><my-component>
  ```
  
  - Component names should be lowercased and dash separated.
  
  > Why? Conforms to native DOM element names.
  
  ```html
  <!-- bad -->
  <Awesome-WebComponent></Awesome-WebComponent>
  
  <!-- good -->
  <awesome-web-component></awesome-web-component>
  ```
  
  - Avoid prefixs shorter than three characters.
  
  ```html
  <!-- bad -->
  <fs-component></fs-component>
  
  <!-- good -->
  <familysearch-component></familysearch-component>
  ```

**[back to top](#table-of-contents)**

## Attributes

  - Use attributes to pass data into the web component.
  
  ```html
  <!-- bad -->
  <script>
  Polymer({
    is: 'familysearch-component',
    ready: function() {
      this.addEventListener('dataChanged', function(e) {
        // do something with the changed data
      });
    }
  });
  </script>
  
  <!-- good -->
  <familysearch-component data="value"></familysearch-component>
  ```
  
  - Attribute names should be lowercased and dash separated.
  
  > Why? Conforms to native DOM attribute names. Also produces camelCased property names in the Polymer object.
  
  ```html
  <!-- bad -->
  <familysearch-component myAttribute="value"></familysearch-component>
  
  <!-- good -->
  <familysearch-component my-attribute="value"></familysearch-component>
  ```
  
  - Boolean values should be based on the existence of the attribute and not its value.
  
  > Why? Conforms to navive DOM attribute properties (e.g. `hidden`, `disabled`).
  
  ```html
  <!-- bad -->
  <familysearch-component boolean-attr="false"></familysearch-component>
  
  <!-- good -->
  <familysearch-component boolean-attr></familysearch-component>
  ```
  
  - Boolean attributes should not prefix the name with words like "is", "show", or "has" as you would in Javascript.
  
  > Why? Conforms to navive DOM attribute property names (e.g. `hidden`, `disabled`).
  
  ```html
  <!-- bad -->
  <familysearch-component is-active></familysearch-component>
  
  <!-- good -->
  <familysearch-component active></familysearch-component>
  ```
  
  - Boolean attributes that remove or disable functionality should be prefixed with the word "no". 
  
  > Why? This isn't a pattern used in native DOM elements but conforms to a Polymer standard (e.g. paper-button uses `noink` to disable the ripple effect).

  ```html
  <!-- bad -->
  <familysearch-component disable-touch></familysearch-component>
  
  <!-- good -->
  <familysearch-component no-touch></familysearch-component>
  ```

**[back to top](#table-of-contents)**

## Events

  - Fire events to pass data out of the web component.
  
  ```html
  <!-- bad -->
  <familysearch-component>
    <!-- don't use bindings to modify a parent elements data -->
    <other-component data={{data}}></other-component> 
  </familysearch-component>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <template>
      <button on-click="handleClick">Click Me</button>
    </template>
  
    <script>
      Polymer({
        is: 'familysearch-component',
        handleClick: function(e, detail) {
          this.fire('dataChanged', {clicked: true});
        }
      });
    </script>
  </dom-module>
  ```
  
  - Event names should have a prefix stronly related to the name of the element.
  
  > Why? Not only will they be uniquely namespaced, but if you use any one of these [event names](https://www.w3.org/TR/shadow-dom/#h-events-that-are-not-leaked-into-ancestor-trees), the event will not propegate through the shadow DOM. 

  ```js
  // bad
  this.fire('error', new Error());
  
  // good
  this.fire('familysearch-component-error', new Error());
  ```
  
  - Event names should end in a [base form](http://grammar.yourdictionary.com/parts-of-speech/verbs/infinitive-verb.html) verb or a noun.
  
  ```js
  // bad 
  this.fire('familysearch-component-data-changed', {});
  
  // good
  this.fire('familysearch-component-data-change', {});
  this.fire('familysearch-component-upload-success', {});
  ```
  
  - Use declaritive event hanlders over JS event handlers (e.g. use the `on-tap` attribute instead of using the `listeners` property or `this.addEventListener`).
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <template>
      <button>Click Me</button>
    </template>
  
    <script>
      Polymer({
        is: 'familysearch-component',
        listeners: {
          tap: 'handleTap'
        },
        handleTap: function(e, detail) {
          console.log('tapped');
        },
        created: function() {
          this.addEventListener('click', function(e) {
            console.log('clicked');
          })
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <template>
      <button on-tap="handleTap">Click Me</button>
    </template>
  
    <script>
      Polymer({
        is: 'familysearch-component',
        handleTap: function(e, detail) {
          console.log('tapped');
        }
      });
    </script>
  </dom-module>
  ```

**[back to top](#table-of-contents)**

## Properties

  - Property names should be camelCased.
  
  > Why? Produces lowercased, dashed separated attribute names in the DOM.
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        properties: {
          'my-var': String
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        properties: {
          myVar: String
        }
      });
    </script>
  </dom-module>
  ```

  - Private properties should be prfixed with an underscore.
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        properties: {
          private: String
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        properties: {
          _private: String
        }
      });
    </script>
  </dom-module>
  ```
  
  - Define constants outside of the Polymer constructor.
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        properties: {
          CONST: {
            type: String,
            value: 'value'
          }
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
      var CONST = 'value';
      
      Polymer({
        is: 'familysearch-component',
      });
    </script>
  </dom-module>
  ```
  
  - Wrap the contents of the script tag inside an immediately-invoked function expression (IIFE).
  
  > Why? To prevent global variables from being created since the script tag is run in the `window` scope.
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
    var isNowGlobal = true;
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
    (function() {
      var isNowGlobal = false;
    })();
    </script>
  </dom-module>
  ```

**[back to top](#table-of-contents)**
  
## Methods

  - Method names should be camelCased.
  
   ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        'my-method': function() {
          // ...
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        myMethod: function() {
          // ...
        }
      });
    </script>
  </dom-module>
  ```

  - Private methods should be prfixed with an underscore.
  
  ```html
  <!-- bad -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        privateMethod: function() {
          // ...
        }
      });
    </script>
  </dom-module>
  
  <!-- good -->
  <dom-module id="familysearch-component">
    <script>
      Polymer({
        is: 'familysearch-component',
        _privateMethod: function() {
          // ...
        }
      });
    </script>
  </dom-module>
  ```

**[back to top](#table-of-contents)**
  
## Bindings

  - Use `dom-if` to conditionally render large portions of the DOM or DOM that will not toogle between hidden/shown states.
  - Use the `hiddden$=` attribute to conditionally render small protions of the DOM or DOM that will toogle between hidden/shown states.
  - Use single bindings `[[ ]]` rather than dobule bindings `{{ }}` 99% of the time.

**[back to top](#table-of-contents)**

## Documentation

  - Use the [Polymer Style Guide](https://polymerelements.github.io/style-guide/#documentation) documentation

**[back to top](#table-of-contents)**
  
## Accessibility

  - Follow web component [accessibility](https://www.polymer-project.org/0.5/articles/accessible-web-components.html) best practices.
  - use ARIA roles when necessary, test for accessibility.

**[back to top](#table-of-contents)**

## Resources

- [Web Component Best Practices](http://webcomponents.org/articles/web-components-best-practices/)
- [Google Web Components Style Guide](https://github.com/GoogleWebComponents/style-guide)

**[back to top](#table-of-contents)**
