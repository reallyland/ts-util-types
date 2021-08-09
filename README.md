<div align="center" style="text-align: center;">
  <h1 style="border-bottom: none;">ts-util-types</h1>

  <p>Commonly used utility types in TypeScript</p>
</div>

<hr />

<a href="https://www.buymeacoffee.com/RLmMhgXFb" target="_blank" rel="noopener noreferrer"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy Me A Coffee" style="height: 20px !important;width: auto !important;" ></a>
[![tippin.me][tippin-me-badge]][tippin-me-url]
[![Follow me][follow-me-badge]][follow-me-url]

[![MIT License][mit-license-badge]][mit-license-url]

> Opinionated utility types in TypeScript

This hosts all the snippets for different utility types that are useful for certain use cases but are not currently supported natively.

## Table of contents <!-- omit in toc -->

- [Pre-requisite](#pre-requisite)
- [Utility types](#utility-types)
  - [Copy\<T\>](#copyt)
  - [DeepNonNullable\<T\>](#deepnonnullablet)
  - [DeepNonReadonly\<T\>](#deepnonreadonlyt)
  - [DeepNullable\<T\>](#deepnullablet)
  - [DeepPartial\<T\>](#deeppartialt)
  - [DeepReadonly\<T\>](#deepreadonlyt)
  - [DeepRequired\<T\>](#deeprequiredt)
  - [ExcludeKey\<T, K\>](#excludekeyt-k)
  - [ExtractKey\<T, K\>](#extractkeyt-k)
  - [Merge\<T, U\>](#merget-u)
  - [Nullish + Nullable\<T\>](#nullish--nullablet)
  - [TupleRecord\<T\>](#tuplerecordt)
  - [Values\<T\>](#valuest)
- [License](#license)

## Pre-requisite

- [TypeScript] >= 4.3.5

## Utility types

### Copy\<T\>

```ts
type Copy<T> = T extends object ? {
    [K in keyof T]: T[K];
} : T;
```

```ts
interface A {
  a: boolean;
  b: number;
  c: string;
}

// {
//   a: boolean;
//   b: number;
//   c: string;
// }
type ACopy = Copy<A>;

// A & { d: boolean }
type B = A & { d: boolean };

// {
//   a: boolean;
//   b: number;
//   c: string;
//   d: boolean;
// }
type BCopy = Copy<B>;
```

### DeepNonNullable\<T\>

```ts
type DeepNonNullable<T> = Copy<NonNullable<{
    [K in keyof T]: DeepNonNullable<T[K]>;
}>>;
```

```ts
// {
//     a: boolean;
//     b: [{
//         c: boolean;
//     }];
//     d: {
//         e: boolean;
//         f: [{
//             g: boolean;
//         }];
//     };
// }
type ADeepNonNullable = DeepNonNullable<DeepNullable<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>>;
```

### DeepNonReadonly\<T\>

```ts
type DeepNonReadonly<T> = Copy<{
    -readonly [K in keyof T]: DeepNonReadonly<T[K]>;
}>;
```

```ts
// {
//     a: boolean;
//     b: [{
//         c: boolean;
//     }];
//     d: {
//         e: boolean;
//         f: [{
//             g: boolean;
//         }];
//     };
// }
type ADeepNonReadonly = DeepNonReadonly<DeepReadonly<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>>;
```

### DeepNullable\<T\>

```ts
type DeepNullable<T, U extends Nullish = null | undefined> = Copy<{
    [K in keyof T]: DeepNullable<T[K], U> | U;
}>;
```

```ts
// {
//     a: boolean | null | undefined;
//     b: [{
//         c: boolean | null | undefined;
//     } | null | undefined] | null | undefined;
//     d: {
//         e: boolean | null | undefined;
//         f: [{
//             g: boolean | null | undefined;
//         } | null | undefined] | null | undefined;
//     } | null | undefined;
// }
type ANullable = DeepNullable<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>;

// {
//     a: boolean | null;
//     b: [{
//         c: boolean | null;
//     } | null] | null;
//     d: {
//         e: boolean | null;
//         f: [{
//             g: boolean | null;
//         } | null] | null;
//     } | null;
// }
type ANullableNull = DeepNullable<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}, null>;

// {
//     a: boolean | undefined;
//     b: [{
//         c: boolean | undefined;
//     } | undefined] | undefined;
//     d: {
//         e: boolean | undefined;
//         f: [{
//             g: boolean | undefined;
//         } | undefined] | undefined;
//     } | undefined;
// }
type ANullableUndefined = DeepNullable<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}, undefined>;
```

### DeepPartial\<T\>

```ts
type DeepPartial<T> = Copy<{
    [K in keyof T]?: DeepPartial<T[K]>;
}>;
```

```ts
// {
//     a?: boolean | undefined;
//     b?: [({
//         c?: boolean | undefined;
//     } | undefined)?] | undefined;
//     d?: {
//         e?: boolean | undefined;
//         f?: [({
//             g?: boolean | undefined;
//         } | undefined)?] | undefined;
//     } | undefined;
// }
type ADeepPartial = DeepPartial<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>;
```

### DeepReadonly\<T\>

```ts
type DeepReadonly<T> = Copy<{
    readonly [K in keyof T]: DeepReadonly<T[K]>;
}>;
```

```ts
// {
//     readonly a: boolean;
//     readonly b: readonly [{
//         readonly c: boolean;
//     }];
//     readonly d: {
//         readonly e: boolean;
//         readonly f: readonly [{
//             readonly g: boolean;
//         }];
//     };
// }
type ADeepReadonly = DeepReadonly<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>;
```

### DeepRequired\<T\>

```ts
type DeepRequired<T> = Copy<{
    [K in keyof T]-?: DeepRequired<T[K]>;
}>;
```

```ts
// {
//     a: boolean;
//     b: [{
//         c: boolean;
//     }];
//     d: {
//         e: boolean;
//         f: [{
//             g: boolean;
//         }];
//     };
// }
type ADeepRequired = DeepRequired<DeepPartial<{
    a: boolean;
    b: [
        {
            c: boolean;
        }
    ];
    d: {
        e: boolean;
        f: [
            {
                 g: boolean;
            }
        ];
    };
}>>;
```

### ExcludeKey\<T, K\>

An aliased of [Exclude] but with typed `K`s in `T`.

```ts
type ExcludeKey<T, K extends keyof T> = Exclude<keyof T, K>;
```

```ts
// 'a' | 'c'
type AExcludeKey = ExcludeKey<{
  a: boolean;
  b: number;
  c: string;
}, 'b'>;
```

### ExtractKey\<T, K\>

An aliased of [Extract] but with typed `K`s in `T`.

```ts
type ExtractKey<T, K extends keyof T> = Extract<keyof T, K>;
```

```ts
// 'b'
type AExtractKey = ExtractKey<{
  a: boolean;
  b: number;
  c: string;
}, 'b'>;
```

### Merge\<T, U\>

```ts
type Merge<T, U> = Copy<Omit<T, keyof U> & U>;
```

```ts
interface A {
  a: boolean;
  b: number;
  c: string;
}

interface B {
  c: boolean;
  d: number;
}

// {
//   a: boolean;
//   b: number;
//   c: boolean;
//   d: number;
// }
type ABMerge = Merge<A, B>;

// {
//   a: boolean;
//   b: number;
//   c: boolean;
//   d: number;
// }
type AMerge = Merge<A, {
  c: boolean;
  d: number;
}>
```

### Nullish + Nullable\<T\>

```ts
type Nullish = null | undefined;
type Nullable<T> = Copy<T | Nullish>;
```

```ts
// number | Nullish
type ANullish = number | Nullish;

// number | null | undefined
type ANullish2 = Copy<number | Nullish>;

// number | null | undefined;
type ANullable = Nullable<number>;
```

### TupleRecord\<T\>

```ts
type TupleRecord<T extends (unknown [] | ReadonlyArray<unknown>)> = {
    keys: T extends infer U ? U : never;
    values: T;
};
```

```ts
// {
//     keys: ["a", "b"];
//     values: ["a", "b"];
// }
type ATupleRecord = TupleRecord<[
    'a',
    'b',
]>

const a2 = ['a', 'b'] as const;

// {
//     keys: readonly ["a", "b"];
//     values: readonly ["a", "b"];
// }
type ATupleRecord2 = TupleRecord<typeof a2>;
```

### Values\<T\>

```ts
type Values<T> = T extends {
    [_ in keyof T]: infer U
} ? U : never;
```

```ts
// boolean | number | string
type AValues = Values<{
  a: boolean;
  b: number;
  c: string;
}>;
```

## License

[MIT License](https://motss.mit-license.org) © Rong Sen Ng

<!-- References -->
[nodejs-url]: https://nodejs.org
[lit-html-url]: https://github.com/PolymerLabs/lit-html
[npm-url]: https://www.npmjs.com
[parse5-url]: https://www.npmjs.com/package/parse5
[pretty-url]: https://www.npmjs.com/package/pretty
[vscode-lit-html-url]: https://github.com/mjbvz/vscode-lit-html
[TypeScript]: https://github.com/Microsoft/TypeScript
[ES Modules]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules
[Exclude]: https://www.typescriptlang.org/docs/handbook/utility-types.html#excludetype-excludedunion
[Extract]: https://www.typescriptlang.org/docs/handbook/utility-types.html#extracttype-union

<!-- MDN -->
[map-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map
[string-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String
[object-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object
[number-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number
[boolean-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean
[html-style-element-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/API/HTMLStyleElement
[promise-mdn-url]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

<!-- Badges -->
[tippin-me-badge]: https://badgen.net/badge/%E2%9A%A1%EF%B8%8Ftippin.me/@igarshmyb/F0918E
[follow-me-badge]: https://flat.badgen.net/twitter/follow/igarshmyb?icon=twitter

[mit-license-badge]: https://flat.badgen.net/npm/license/lit-ntml

[coc-badge]: https://flat.badgen.net/badge/code%20of/conduct/pink

<!-- Links -->
[tippin-me-url]: https://tippin.me/@igarshmyb
[follow-me-url]: https://twitter.com/igarshmyb?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=motss/lit-ntml

[mit-license-url]: https://github.com/motss/lit-ntml/blob/master/LICENSE

[coc-url]: https://github.com/motss/lit-ntml/blob/master/code-of-conduct.md
