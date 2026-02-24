# three-wgsl-template-node

Prototype for [template tag function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) integration with three.js' "wgsl" and "glsl" nodes function and code snippet nodes to enable more intuitive declaration of shader code while continuing to enable benefits and integrating with the dependency graph of three.js' TSL.

```js
// before
wgslFn( /* wgsl */`
  fn traverse( ray: Ray ) -> RayIntersection {

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

    let hit = ${ intersectsBoundsFn( 'ray' ) };
    let vertex0 = ${ attributesStorage }[ hit.indices.x ];

    // ...
    return hit;

  }
`;
```

## Features

### WgslTagFn & WgslTgCode

#### Structs

Structs definitions can be used in string-literal wgsl functions without relying on hardcoded variable names.

```js
const rayStruct = struct( {
  origin: 'vec3f',
  direction: 'vec3f',
} );

wgslFn/* wgsl */`
  fn transformRay( ray: ${ rayStruct }, mat: mat4x4f ) -> ${ rayStruct } {

    var resultRay: Ray;
    resultRay.origin = ( mat * vec4( ray.origin, 1 ) ).xyz;
    resultRay.direction = ( mat * vec4( ray.direction, 0 ) ).xyz;
    return resultRay;

  }
`;
```

#### Buffers & Arrays

Storage buffers can be used without making assumptions about structure of the shader variable layout.

```js
const myStorageBuffer = storage( new StorageBufferAttribute( new Float32Array( arr ), 4 ), 'vec4f' );

wgslTagFn`
  fn fn( globaId: vec3u ) -> void {

    let value = ${ myStorageBuffer }[ globalId.x ];

  }
`;
```

#### Functions

Function names or calls can be integrated using template literals that are evaluated & inlined on node material construction. Functions can be used to reference local variables using string literals of local variables in the function or referencing other availabe TSL variable nodes.

```js
wgslTagFn/* wgsl */`
  fn compute() -> void {
  
    let input = 10.0;
    let result = ${ myFunc }( input );
  
    // or
  
    let result = ${ myFun( { varName: 'input' } ) };
  
  }
`;
```

#### Variable Includes

Other includes for dependencies that are relied on for hardcoded variable names can continue to be included using arrays.

```js
const PI = float( 3.14159 );

const constants = wgsl( `
  let PI = 3.14159;
` );

wgslTagFn/* wgsl */`

  ${ [ constants ] }
  
  fn compute() -> void {
  
    // PI is availale from the 
    let result = func( PI );
  
    // or
  
    let result = func( ${ PI } );
  
  }
`;
```

#### Code Snippets

Similar to `wgsl` nodes for representing node snippets, `wgslTagCode` can be used to construct code snippets with the same dependency systems.

```js
const inlineAdd = ( name, value ) => wgslTagCode`${ name } += ${ value };`;

wgslTagFn/* wgsl */`
  fn compute() -> void {

    var x = 10;
    ${ inlineAdd( 'x', 5 ) }

  }
`;
```
