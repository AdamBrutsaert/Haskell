## Definition

A **functor** is a [[typeclass]] acting on a type of [[kind]] `* -> *`. It exposes two methods named `fmap` and `(<$)`. The first one is used to apply a function to the content of the functor, while the second one is used to replace the content of the functor with a constant value.

One can see a functor as a generic container.
## Implementation

```haskell
type Functor :: (* -> *) -> Constraint
class Functor f where
	fmap :: (a -> b) -> f a -> f b
	(<$) :: a -> f b -> f a

	(<$) = fmap . const
	{-# MINIMAL fmap #-}
```

The infix operator of `fmap` is `<$>`.
The flipped version of `<$` is `$>`.

Examples of types that are instances of `Functor`:
- `[]`
- `Maybe`
- `Either e`
- `IO`
- `((->) r)`
## Laws

A functor must respect the following laws:
- **Identity**: `fmap id = id`
- **Composition**: `fmap (f . g) = fmap f . fmap g`

These are not enforced by the compiler, so one must be careful when implementing a new instance of `Functor`.

## Examples

### List

```haskell
instance Functor [] where
	fmap _ [] = []
	fmap f (x:xs) = f x : fmap f xs
```

```haskell
fmap (+1)  [1, 2, 3] -- [2, 3, 4]
(*) <$> [1, 2, 3] -- [(*1), (*2), (*3)]
'a' <$ replicate 3 undefined -- "aaa"
```

### Maybe

```haskell
instance Functor Maybe where
	fmap _ Nothing = Nothing
	fmap f (Just x) = Just (f x)
```

```haskell
fmap (+1) (Just 1) -- Just 2
(+1) <$> Nothing -- Nothing
fmap (+) (Just 1) -- Just (+1)
3 <$ Just 5 -- Just 3
```

### Either a

```haskell
instance Functor (Either a) where
	fmap _ (Left x) = Left x
	fmap f (Right y) = Right (f y)
```

```haskell
fmap (+1) (Right 1) -- Right 2
(+1) <$> (Left "error") -- Left "error"
fmap (+) (Right 1) -- Right (+1)
3 <$ Right 5 -- Right 3
Left "error" $> 3  -- Left "error"
```

### IO

```haskell
instance Functor IO where
	fmap f io = io >>= (return . f)
```

```haskell
foo :: Num a => IO a
foo = return 1

fmap (+1) foo -- IO 2
(+) <$> foo -- IO (+1)
3 <$ foo -- IO 3
```

### ((->) r)

```haskell
instance Functor ((->) r) where
	fmap = (.)
```

```haskell
fmap (+1) (*2) 3 -- 7
(+1) <$> (*2) -- \x -> (+1) ((*2) x)
3 <$ (*2) $ 3 -- 3
(*2) $> 3 -- \_ -> 3
```
