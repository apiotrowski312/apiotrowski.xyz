---
title: "Unit testing Go functions with golden files"
description: ""
date: 2020-12-17T12:20:05+01:00
slug: ""
tags: ["go","golden","unit","testing"]
categories: ["package", "go"]
externalLink: ""
disable_comments: true
---

## What is golden file testing? 

With usual unit tests, you write down expected output into unit test (as showed below). This way is perfectly fine and works really good most of the time. However, in my last project I had to use custom big data structure (hundreds nodes in tree), and then I asked myself, how should I test NewTree() function? Writing down expected output would be tedious. That is where golden file testing come handy. You don't need to write any expected output down, every suite case has separate file with expected output. Golden files are stored in chosen directory (Usually directory is next to test file with name "testdata").

Usually, unit test looks like this:

```golang
func TestCreateTree(t *testing.T) {
	tests := []struct {
		name     string
		size     int
		expected Node
	}{
		{"Simple example of JSON", 100,
			Node{
				Value: 0,
				Children: Node{
					{
						"Value": 1,
						"Children": [...],
					},
					...
				},
			}.
		}
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			got := CreateTree(tt.size)

			goldtest.AssertJSON(t, got, tt.filename)
		})
	}
}
```

and below you can see how it would look like with golden file (using my goldtest package):

```golang
func TestCreateTree(t *testing.T) {
	tests := []struct {
		name     string
		size     int
		filename string
	}{
		{"Simple example of JSON", 100, "testdata/node100"},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			got := CreateTree(tt.size)

			goldtest.AssertJSON(t, got, tt.filename)
		})
	}
}
```

You can see that this is a lot cleaner, easier to read, and I think the most important: easier to maintenance!

## goldtest package

I have created package [goldtest](https://github.com/apiotrowski312/goldtest), that will help you with this type of testing.

Usage is really simple (as showed in example above), you just have to call **Assert** or **AssertJSON** function, that is basically all you need to do. There is only two Assert functions as I didn't need anything else, if you feel there is something else, that would fit your case, feel free to leave PR or open an issue :D

### Assert

Assert function works based on converting any interface into string and then into bytes, so it can be saved into a file. This approach works well most of the time, bonus points for checking not exported fields also. Biggest disagvantage of this approach is low readability on the goldenfile. With smaller structs its not an issue.

### AssertJSON

AssertJSON function works based on Marshal function. There is two possible ways to test all data you want. You can export every field that you want to test with goldenfile, this is easy to do approach, however not always wanted. Other way is to create your own Marshal function (Example [HERE](http://choly.ca/post/go-json-marshalling/) and [HERE](https://medium.com/@dynastymasra/override-json-marshalling-in-go-cb418102c60f)). Big plus of this function is well structured and readable JSON that everybody knows.

## Further reading
- https://ieftimov.com/post/testing-in-go-golden-files/
- https://medium.com/@jarifibrahim/golden-files-why-you-should-use-them-47087ec994bf
- https://github.com/apiotrowski312/goldtest