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
  - [Cloned\<T\>](#clonedt)
  - [Combinations\<T\>](#combinationst)
  - [DeepExecWith\<T\>](#deepexecwitht)
  - [DeepNonNullable\<T\>](#deepnonnullablet)
  - [DeepNonReadonly\<T\>](#deepnonreadonlyt)
  - [DeepNullable\<T\>](#deepnullablet)
  - [DeepPartial\<T\>](#deeppartialt)
  - [DeepReadonly\<T\>](#deepreadonlyt)
  - [DeepRequired\<T\>](#deeprequiredt)
  - [Entries\<T\>](#entriest)
  - [ExcludeKey\<T, K\>](#excludekeyt-k)
  - [ExtractKey\<T, K\>](#extractkeyt-k)
  - [Flatten\<T\>](#flattent)
  - [Merge\<T, U\>](#merget-u)
  - [Nullish + Nullable\<T, U\>](#nullish--nullablet-u)
  - [OmitKey\<T\>](#omitkeyt)
  - [Pop\<T\>](#popt)
  - [Shift\<T\>](#shiftt)
  - [ShiftUntil\<T, Key\>](#shiftuntilt-key)
  - [TupleRecord\<T\>](#tuplerecordt)
  - [Unshift\<T, Elements\>](#unshiftt-elements)
  - [Values\<T\>](#valuest)
- [License](#license)

## Pre-requisite

- [TypeScript] >= 4.3.5

## Utility types

### Cloned\<T\>

```ts
type Cloned<T> = DeepExecWith<T, {
    [K in keyof T]: Cloned<T[K]>;
}>;
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
type ACloned = Cloned<A>;

// A & { d: boolean }
type B = A & { d: boolean };

// {
//   a: boolean;
//   b: number;
//   c: string;
//   d: boolean;
// }
type BCloned = Cloned<B>;
```

### Combinations\<T\>

```ts
type _CombinationsUtil<T extends readonly unknown[], _Key = T[number]> =
  _Key extends _Key
    ? [_Key] | (
      IsEmptyArray<ShiftUntil<T, _Key>> extends true
        ? never
        : [_Key, ..._CombinationsUtil<ShiftUntil<T, _Key>>]
    )
    : never;

type Combinations<T extends readonly unknown[]> =
  T extends readonly unknown[]
    ? T extends unknown[]
      ? _CombinationsUtil<T>
      : Readonly<_CombinationsUtil<T>>
    : never;
```

```ts
type ACombinations = Combinations<readonly []>; // never
type BCombinations = Combinations<[]>; // never

type CCombinations = Combinations<readonly ['a']>; // readonly ['a']
type DCombinations = Combinations<['a']>; // ['a']

type ECombinations = Combinations<readonly ['a', 'b']>; // readonly ['a'] | readonly ['b'] | readonly ['a', 'b']
type FCombinations = Combinations<['a', 'b']>; // ['a'] | ['b'] | ['a', 'b']

type GCombinations = Combinations<readonly ['a', 'b', 'c']>; // readonly ['a'] | readonly ['b'] | readonly ['c'] | readonly ['a', 'b'] | readonly ['a', 'c'] | readonly ['b', 'c'] | readonly ['a', 'b', 'c']
type HCombinations = Combinations<['a', 'b', 'c']>; // ['a'] | ['b'] | ['c'] | ['a', 'b'] | ['a', 'c'] | ['b', 'c'] | ['a', 'b', 'c']
```

### DeepExecWith\<T\>

```ts
type DeepExecWith<T, Condition> = T extends Record<string, unknown>
    ? Condition
    : T extends (...args: any) => unknown
        ? T
        : Condition;
```

```ts
type NeverPrimitives<T> = DeepExecWith<T, {
    [K in keyof T]: T[K] extends object ? NeverPrimitives<T[K]> : never;
}>;

// {
//     a: () => void;
//     b: {
//         c: never;
//         d: [never, never, never];
//         e: (a?: string | undefined) => Promise<void>;
//         f: never;
//         g: never;
//         h: never;
//         i: {
//             toString: () => string;
//             valueOf: () => symbol;
//             readonly description: never;
//         };
//         j: {
//             toString: (radix?: number | undefined) => string;
//             toLocaleString: (locales?: string | undefined, options?: BigIntToLocaleStringOptions | undefined) => string;
//             valueOf: () => bigint;
//         };
//         k: never;
//     };
//     c: never;
//     d: [never, never, never];
//     e: (a?: string | undefined) => Promise<void>;
//     f: never;
//     g: never;
//     h: never;
//     i: {
//         toString: () => string;
//         valueOf: () => symbol;
//         readonly description: never;
//     };
//     j: {
//         toString: (radix?: number | undefined) => string;
//         toLocaleString: (locales?: string | undefined, options?: BigIntToLocaleStringOptions | undefined) => string;
//         valueOf: () => bigint;
//     };
//     k: never;
// }
type ANeverPrimitives = NeverPrimitives<{
    a(): void;
    b: {
        c: 1;
        d: [1, 2, 3];
        e(a?: string): Promise<void>;
        f: '1';
        g: null;
        h: undefined;
        i: Symbol;
        j: BigInt;
        k: true;
    };
    c: 1;
    d: [1, 2, 3];
    e(a?: string): Promise<void>;
    f: '1';
    g: null;
    h: undefined;
    i: Symbol;
    j: BigInt;
    k: true;
}>;
```

### DeepNonNullable\<T\>

```ts
type DeepNonNullable<T> = Cloned<DeepExecWith<T, NonNullable<{
    [K in keyof T]: DeepNonNullable<T[K]>;
}>>>;
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
type DeepNonReadonly<T> = Cloned<DeepExecWith<T, {
    -readonly [K in keyof T]: DeepNonReadonly<T[K]>;
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
type DeepNullable<T, U extends Nullish = null | undefined> = Cloned<DeepExecWith<T, {
    [K in keyof T]: DeepNullable<T[K], U> | U;
}>>;
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
type DeepPartial<T> = Cloned<DeepExecWith<T, {
    [K in keyof T]?: DeepPartial<T[K]>;
}>>;
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
type DeepReadonly<T> = Cloned<DeepExecWith<T, {
    readonly [K in keyof T]: DeepReadonly<T[K]>;
}>>;
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
type DeepRequired<T> = Cloned<DeepExecWith<T, {
    [K in keyof T]-?: DeepRequired<T[K]>;
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

### Entries\<T\>

```ts
type Entries<T> = {
    [K in keyof T]: [K, T[K]];
}[keyof T][];
```

```ts
// (["a", "a1"] | ["b", "b1"])[]
type AEntries = Entries<{
    a: 'a1',
    b: 'b1',
}>;
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

### Flatten\<T\>

```ts
type Flatten<T extends readonly unknown[]> =
  T extends readonly [infer ReadonlyFirst, ...infer ReadonlyRest]
    ? T extends [infer First, ...infer Rest]
      ? [
          ...(
            First extends readonly unknown[]
                ? Flatten<First>
                : [First]
          ),
          ...Flatten<Rest>
        ]
      : Readonly<[
          ...(
            ReadonlyFirst extends readonly unknown[]
                ? Flatten<ReadonlyFirst>
                : Readonly<[ReadonlyFirst]>
          ),
          ...Flatten<ReadonlyRest>
        ]>
    : T;
```

```ts
// ['a', 'b', 'c']
type AFlatten = Flatten<[['a'], 'b', [['c']]]>;
type BFlatten = Flatten<[['a'], 'b', [readonly ['c']]]>;

// readonly ['a', 'b', 'c']
type CFlatten = Flatten<readonly [['a'], 'b', [['c']]]>;
type DFlatten = Flatten<readonly [['a'], 'b', readonly [readonly ['c']]]>;
type EFlatten = Flatten<readonly [readonly ['a'], 'b', [['c']]]>;
type FFlatten = Flatten<readonly [readonly ['a'], 'b', [readonly ['c']]]>;
type GFlatten = Flatten<readonly [readonly ['a'], 'b', readonly [readonly ['c']]]>;
type HFlatten = Flatten<[readonly ['a'], 'b', [readonly ['c']]]>;
```

### Merge\<T, U\>

```ts
type Merge<T, U> = Cloned<Omit<T, keyof U> & U>;
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

### Nullish + Nullable\<T, U\>

```ts
type Nullish = null | undefined;

// Workaround to expand Nullish with U extends Nullish
type Nullable<T, U extends Nullish = Nullish> = Cloned<U extends Nullish ? T | U : T | U>;
```

```ts
// number | Nullish
type ANullish = number | Nullish;

// number | null | undefined
type ANullish2 = Cloned<number | Nullish>;

// number | null | undefined;
type ANullable = Nullable<number>;

// number | null;
type ANullableNull = Nullable<number, null>;

// number | undefined;
type ANullableUndefined = Nullable<number, undefined>;
```

### OmitKey\<T\>

```ts
type OmitKey<T, K extends keyof T> = Omit<T, K>;
```

```ts
interface A {
    a: boolean;
    b: number;
    c: string;
}

// {
//     a: boolean;
//     c: string;
// }
type AOmitKey = OmitKey<A, 'b'>;

// {
//     c: string;
// }
type BOmitKey = OmitKey<A, 'b' | 'a'>;
```

### Pop\<T\>

```ts
type Pop<T extends readonly unknown[]> =
  T extends readonly [...infer _ReadonlyRest, infer ReadonlyLast]
    ? T extends [...infer _Rest, infer Last]
      ? Last
      : Readonly<ReadonlyLast>
    : never;
```

```ts
type APop = Pop<['a']>; // 'a'
type BPop = Pop<['a', 'b']>; // 'b'
type CPop = Pop<readonly ['a']>; // 'a'
type DPop = Pop<readonly ['a', 'b']>; // 'b'
```

### Shift\<T\>

```ts
type Shift<T extends readonly unknown[]> =
    T extends readonly [infer _ReadonlyFirst, ...infer ReadonlyRest]
        ? T extends [infer _First, ...infer Rest]
            ? Rest
            : Readonly<ReadonlyRest>
        : [];
```

```ts
type AShift = Shift<readonly ['a']>; // readonly []
type BShift = Shift<readonly ['a', 'b']>; // readonly ['b']
type CShift = Shift<readonly ['a', 'b', 'c']>; // readonly ['b', 'c']

type AShift2 = Shift<['a']>; // []
type BShift2 = Shift<['a', 'b']>; // ['b']
type CShift2 = Shift<['a', 'b', 'c']>; // ['b', 'c']
```

### ShiftUntil\<T, Key\>

```ts
type ShiftUntil<T extends readonly unknown[], Key extends T[number]> =
  T extends readonly [infer ReadonlyFirst, ...infer ReadonlyRest]
    ? T extends [infer First, ...infer Rest]
      ? First extends Key
        ? Rest
        : ShiftUntil<Rest, Key>
      : ReadonlyFirst extends Key
        ? Readonly<ReadonlyRest>
        : ShiftUntil<Readonly<ReadonlyRest>, Key>
    : T;
```

```ts
type AShiftUntil = ShiftUntil<readonly ['a'], 'a'>; // readonly []
type AShiftUntil2 = ShiftUntil<['a'], 'a'>; // []

type BShiftUntil = ShiftUntil<readonly ['a', 'b'], 'a'>; // readonly ['b']
type BShiftUntil2 = ShiftUntil<readonly ['a', 'b'], 'b'>; // readonly []
type BShiftUntil3 = ShiftUntil<['a', 'b'], 'a'>; // ['b']
type BShiftUntil4 = ShiftUntil<['a', 'b'], 'b'>; // []

type CShiftUntil = ShiftUntil<readonly ['a', 'b', 'c'], 'a'>; // readonly ['b', 'c']
type CShiftUntil2 = ShiftUntil<readonly ['a', 'b', 'c'], 'b'>; // readonly ['c']
type CShiftUntil3 = ShiftUntil<readonly ['a', 'b', 'c'], 'c'>; // readonly []
type CShiftUntil4 = ShiftUntil<['a', 'b', 'c'], 'a'>; // ['b', 'c']
type CShiftUntil5 = ShiftUntil<['a', 'b', 'c'], 'b'>; // ['c']
type CShiftUntil6 = ShiftUntil<['a', 'b', 'c'], 'c'>; // []
```

### TupleRecord\<T\>

```ts
type TupleRecord<T extends (unknown [] | ReadonlyArray<unknown>)> = DeepReadonly<{
    keys: T[number];
    values: T;
}>;
```

```ts
// {
//     readonly keys: "a" | "b";
//     readonly values: readonly ["a", "b"];
// }
type ATupleRecord = TupleRecord<[
    'a',
    'b',
]>

const a2 = ['a', 'b'] as const;

// {
//     readonly keys: "a" | "b";
//     readonly values: readonly ["a", "b"];
// }
type ATupleRecord2 = TupleRecord<typeof a2>;
```

### Unshift\<T, Elements\>

```ts
type Unshift<T extends readonly unknown[], Elements extends readonly unknown[]> =
    T extends readonly unknown[]
        ? T extends unknown[]
          ? [...Elements, ...T]
          : Readonly<[...Elements, ...T]>
        : T;
```

```ts
type AUnshift = Unshift<['a'], ['b']>; // ['b', 'c']
type BUnshift = Unshift<['a'], ['b', 'c']>; // ['b', 'c', 'a']
type CUnshift = Unshift<readonly ['a'], ['b']>; // readonly ['b', 'a']
type DUnshift = Unshift<readonly ['a'], ['b', 'c']>; // readonly ['b', 'c', 'a']
type EUnshift = Unshift<['a'], readonly ['b']>; // ['b', 'a']
type FUnshift = Unshift<['a'], readonly ['b', 'c']>; // ['b', 'c', 'a']
type GUnshift = Unshift<readonly ['a'], readonly ['b']>; // readonly ['b', 'a']
type HUnshift = Unshift<readonly ['a'], readonly ['b', 'c']>; // readonly ['b', 'c', 'a']
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
