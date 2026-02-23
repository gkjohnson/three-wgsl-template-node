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
}`, [
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
}`;
```
