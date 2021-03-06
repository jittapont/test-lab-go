# go-env

[![Build Status](https://travis-ci.com/zoftdev/go-env.svg?branch=master)](https://travis-ci.com/zoftdev/go-env)
[![GoDoc](https://godoc.org/github.com/zoftdev/go-env?status.svg)](https://godoc.org/github.com/zoftdev/go-env)
[![zoftdevOSS Lifecycle](https://img.shields.io/osslifecycle/zoftdev/go-expect.svg)]()


Package env provides an `env` struct field tag to marshal and unmarshal environment variables.

## Usage

```go
package main

import (
	"log"
	"time"

	env "github.com/zoftdev/go-env"
)

type Environment struct {
	Home string `env:"HOME"`

	Jenkins struct {
		BuildId     *string `env:"BUILD_ID"`
		BuildNumber int     `env:"BUILD_NUMBER"`
		Ci          bool    `env:"CI"`
	}

	Node struct {
		ConfigCache *string `env:"npm_config_cache,NPM_CONFIG_CACHE"`
	}

	Extras env.EnvSet

	Duration      time.Duration `env:"TYPE_DURATION"`
	DefaultValue  string        `env:"MISSING_VAR,default=default_value"`
	RequiredValue string        `env:"IM_REQUIRED,required=true"`
}

func main() {
	var environment Environment
	es, err := env.UnmarshalFromEnviron(&environment)
	if err != nil {
		log.Fatal(err)
	}
	// Remaining environment variables.
	environment.Extras = es

	// ...

	es, err = env.Marshal(environment)
	if err != nil {
		log.Fatal(err)
	}

	home := "/tmp/edgarl"
	cs := env.ChangeSet{
		"HOME":         &home,
		"BUILD_ID":     nil,
		"BUILD_NUMBER": nil,
	}
	es.Apply(cs)

	environment = Environment{}
	err = env.Unmarshal(es, &environment)
	if err != nil {
		log.Fatal(err)
	}

	environment.Extras = es
}
```
