# three-wgsl-template-node

Prototype for [template tag function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) integration with three.js' "wgsl" and "glsl" nodes function and code snippet nodes to enable more intuitive declaration of shader code while continuing to enable benefits and integrating with the dependency graph of three.js' TSL.

```js
// before
wgslFn( /* wgsl */`
  fn traverse( ray: Ray ) -> RayIntersection {
    // ...    
    let hit = interesectsBounds( ray );
    let vertex0 = attributes.value[ hit.indices.x ];
    // ...
    return hit;
  }
`, [
  rayStruct, intersectionStruct, attributesStruct,
  attributesStorage, intersectsBoundsFn, constants
] );

// after
wgslTagFn`
  // raw includes
  ${ [ constants ] }
  
  // fn
  fn traverse( ray: ${ rayStruct } ) -> ${ intersectionStruct } {
    // ...    
    let hit = ${ intersectsBoundsFn( 'ray' ) };
    let vertex0 = ${ attributesStorage }[ hit.indices.x ];
    // ...
    return hit;
  }
`;
```

## Features

### Functions

Function names or calls can be integrated using template literals that are evaluated & inlined on node material construction. Functions can be used to reference local variables using string literals of local variables in the function or referencing other availabe TSL variable nodes.

```
wgslFn( /* wgsl */`
  fn compute() -> void {
  
    let input = 10.0;
    let result = ${ myFunc }( input );
  
    // or
  
    let result = ${ myFun( { varName: 'input' } ) };
  
  }
` );
```

### Variable Includes

Other includes for dependencies that are relied on for hardcoded variable names can continue to be included using arrays.

```
const PI = float( 3.14159 );

const constants = wgsl( `
  let PI = 3.14159;
` );

wgslFn( /* wgsl */`

  ${ [ constants ] }
  
  fn compute() -> void {
  
    // PI is availale from the 
    let result = func( PI );
  
    // or
  
    let result = func( ${ PI } );
  
  }
` );
```

