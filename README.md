# UrlWithoutSchemeValidator Grails Plugin

## Summary
The Url Without Scheme Validator Plugin allows to use custom validator that validates the URLs but - different than standard validator - does not care if the scheme (ex. http, ftp) is provided or not. Moreover, it can be used like "first-class", built-in validator.

## Instalation
Add the following to your _grails-app/conf/BuildConfig.groovy_

	…
	plugins {
	…
		 compile ':url-without-scheme-validator:0.3'
	…
	}

## Description

### Purpose

It's a common requirement to save the URL a user provides. The standard Grails validatior accepts only URLs with scheme, like

	http://www.google.com

But users often provide the URL without scheme, ie.

	www.google.com

The goal of this plugin is to provide a URL validator that allows you to validate the URL, no matter if the scheme was provided or not.


### Usage

The plugin can be used the same way as [built-in domain-level validators](http://grails.org/doc/latest/ref/Domain%20Classes/constraints.html), like `url: true`, `blank: false`, etc.

So to make sure the URL is valid, no matter if user put the scheme or not, just use `urlWithoutScheme: true`.

Example:

	package com.example

	class Domain {
	
		String url
	
		static constraints = {
		    url urlWithoutScheme: true
		}
	
	}

### Retrieving valid URL (with scheme)

The validator allows users to save URLs without scheme, which is perfect for displaying. However, the URL without scheme is not usable in, for example, HTML A tag:

	<a href="www.google.com">Google</a> // not a proper URL

That's why the plugin contains the util method that prefix the given URL with scheme, but only if it is necessary. Otherwise, it returns the input as it is. The method has the following signature:

	String prefixSchemeIfNecessary(final String url, final UrlScheme scheme = HTTP)

The default scheme is `UrlScheme.HTTP`, but `UrlScheme.HTTPS` and `UrlScheme.FTP` are also available.

The example usage could look like:

	package com.example

	class Domain {

		static transients = ['httpUrl', 'ftpUrl']

		String url

		static constraints = {
		    url nullable: true, urlWithoutScheme: true
		}

		String getHttpUrl() {
			UrlValidatorUtils.prefixSchemeIfNecessary(url) // prefixes url with http scheme, if no scheme is provided
		}

		String getFtpUrl() {
			UrlValidatorUtils.prefixSchemeIfNecessary(url, UrlScheme.FTP) // prefixes url with ftp scheme, if no scheme is provided
		}

	}

### Internationalization (i18n)

The default error message for the validator is 

	default.url.without.schema.not.valid.message=Property [{0}] of class [{1}] with value [{2}] is not a valid URL

It's also possible to configure it on a field level, by creating an entry in `messages.properties` with suffix: 

	not.urlWithoutScheme

So the message.properties entry for the class above could look like:

	com.example.Domain.url.not.urlWithoutScheme={2} is not a valid URL

And result in error message saying for example:
<pre>
     <s>www.google</s>www.invalidtld is not a valid URL (.google is valid since version 0.3 (like .beer, .vodka and .pizza)
<pre>

