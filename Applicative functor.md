## Definition

An **applicative functor**, or **applicative** for short, is a [[typeclass]] acting on a type of [[kind]] `* -> *` that is already a [[Functor|functor]]. It exposes multiple methods, but the most important are `pure` and `(<*>)`. The first one is used to inject a value into the functor, while the second one is used to apply a function contained in a functor to the content of another functor.

One can see an applicative as a beefed up functor, i.e. with additional capabilities.

## Implementation

```haskell
type Applicative :: (* -> *) -> Constraint
class Functor f => Applicative f where
	pure :: a -> f a
	(<*>) :: f (a -> b) -> f a -> f b
	liftA2 :: (a -> b -> c) -> f a -> f b -> f c
	(*>) :: f a -> f b -> f b
	(<*) :: f a -> f b -> f a

	(<*>) = liftA2 id
	liftA2 f x y = f <$> x <*> y
	u *> v = (id <$ u) <*> v
	u <* v = liftA2 const u v
	{-# MINIMAL pure, ((<*>) | liftA2) #-}
```

## Laws

An applicative functor must respect the following laws:
- **Identity**: `pure id <*> v = v`
- **Composition**: `pure (.) <*> u <*> v <*> w = u <*> (v <*> w)`
- **Homomorphism**: `pure f <*> pure x = pure (f x)`
- **Interchange**: `u <*> pure y = pure ($ y) <*> u`
