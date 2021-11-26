---
title: "Echo Middleware for easy authentication and authorization"
description: ""
date: 2021-11-26
tags:
- "echo"
- "go"
---

In this a short article, as I wanted to share with you an ideal use-case for a middleware.

Middleware is a function that runs between user requests and applications code. It can do whatever you code it to do. In my example it will authenticate the user and pass custom context to an actual micro-service. This way there is no need for special logic in a service to handle auth and it will already have all the information it needs for it to work.

## Straight to the code

Creating custom context is quite easy. Just create a new structure and embed `echo.Context`, this way we do not need to do anything to implement `Context` interface.
We could just export `userID` but I want to make sure nothing will overwrite `userID`.

```go
type UserContext struct {
	echo.Context
	userID int64
}

func (c *UserContext) GetUserID() int64 {
	return c.userID
}
```

Next step is to implement Middleware function. It should get and return `HandlerFunc` just like every other `MiddlewareFunc` in echo.
```go
type (
  MiddlewareFunc func(HandlerFunc) HandlerFunc
  HandlerFunc func(Context) error
)
```

And this is an implementation of a function. It is required to pass `authenticator` which is needed to do the actual authentication.

```go
type authenticator interface {
	AuthenticateUser(ctx context.Context, token string) (int64, error)
}

func AuthenticateUserMiddleware(authenticator authenticator) echo.MiddlewareFunc {
	return func(next echo.HandlerFunc) echo.HandlerFunc {
		return func(c echo.Context) error {
			// get token from request
			authToken := c.Request().Header.Get("Authorization")
			if authToken == "" {
				return echo.NewHTTPError(http.StatusUnauthorized, "No authorization token")
			}

			// authenticate user and get user ID.
			userID, err := authenticator.AuthenticateUser(c.Request().Context(), authToken)
			if err != nil {
				return echo.NewHTTPError(http.StatusUnauthorized, "Invalid authorization token")
			}

			// create new, custom context
			uctx := &UserContext{
				Context: c,
				userID:  userID,
			}

			// pass new context to further
			return next(uctx)
		}
	}
}
```

## How to use this middleware and Context


You can add your middleware to each GET/POST/etc.

```go
echo := echox.CreateEchoServer()
echo.GET("/:id", func(c echo.Context) error {
	return s.getRecipe(c)
}, AuthenticateUserMiddleware(s.authenticator))
```

To use custom context you should do type casting and check if the type is correct.

```go
func (s recipe) getRecipe(c echo.Context) error {
	uc, isOk := c.(*echox.UserContext)
	if !isOk {
		return echo.NewHTTPError(http.StatusInternalServerError, "wrong context type")
	}

	userID := uc.GetUserID()
	...
}
```

## Summary

Middlewares are really handy tools, so I hope this article helped you a bit and now you understand how middlewares work and how to implement one.

## Read more

- https://echo.labstack.com/middleware/

### Random wisdom

There are more trees on Earth than stars in our galaxy.

- https://www.plant-for-the-planet.org/media/files/press/27fbf376-nydailynews-there-are-more-trees-on-earth-than-anyone-thought.pdf
- https://www.snopes.com/fact-check/trees-stars-milky-way/