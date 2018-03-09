# Easy Content-Security-Policy Package for Umbraco

This package for [Umbraco](https://umbraco.com) adds easily-configured Content Security Policy (CSP) headers to your website. The Content-Security-Policy header's use is to prevent events like cross-site scripting, clickjacking, and other code injection attacks that might be executed by malitious content that your website trusts. Let's make it only trust what we want it to!

Since most added scripts, fonts, images, objects, or other items that would be limited by a Content-Security-Policy header are added by front-end developers, the goal of this package is to make it easy for a developer to add new policies when needed without having to parse complicated web.config files or dive into a custom HttpModule (which is what this package makes for you!).

## Installation & Use

You can install The Easy Content-Security-Policy Package to your Umbraco project using either the Umbraco package installer or by downloading and installing it locally from the package on the Our package repo.

### The ContentSecurityPolicies.config File

After installing the package, you'll note that a new configuration file has been added to your project at `/Configs/ContentSecurityPolicies.config`, where there is an example policy, a policy set specifically for the Umbraco backoffice, as well as some documentation right there for you so you don't have to hunt it down. But let's go through it anyway, shall we?

#### Adding a new Policy

The `ContentSecurityPolicies.config` holds a set of `<Policy>` nodes. These nodes can be added with or without a `location` attribute. Examples of this could be:

```xml

<Policy /> <!-- This displays on every domain and every folder for this Umbraco application -->
<Policy location="https://offroadcode.com"> <!-- This will add this security policy only to calls made OR referred by this domain -->
<Policy location="/umbraco"> <!-- This will add this security policy for this specific section of the site, such as the backoffice -->

```

_**_Note:_** The order in which you add these matters! If you have a folder policy above a domain policy, the domain policy will override the folder policy. Make sure you put them in the proper order!_

#### Adding a new Source

A `<Policy>` can have a number of `<Source>`s, which can map to any of the available options browsers support for CSPs. You can view a full list of browser support on [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP). Each `<Source>` requires a name attribute to be declared, as well:

```xml
<Source name="default-src">
```

#### Adding a new Allowed Call to Your Source

Inside the `<Source>` tag (which again, has a name attribute), you can now declare any of the different allowed policies, including some of the following like:

```xml
<Allow>'self'</Allow>
<Allow>'unsafe-inline'</Allow>
<Allow>'unsafe-eval'</Allow>
<Allow>data:</Allow>
<Allow>*.adomainyouneed.com</Allow>
```

These are just examples, you can also use hashes or any other types of policies that you need; they'll all get added properly :)

_**_Note:_** Remember that if you add a new source with new allowed items and your `default-src` is set, it will **_override_** the `default-src`, not append it. So if you have `'self'` as one of your allowed calls on default, you'll need to add it to the others (unless you expressly do not wish them to use it)._

#### Full Example

A simple but complete Content-Security-Policy might look like this:

```xml
<Policy>
    <Source name="default-src">
        <Allow>'self'</Allow>
        <Allow>data:</Allow>
    </Source>
    <Source name="style-src">
        <Allow>'self'</Allow>
        <Allow>'unsafe-inline'</Allow>
        <Allow>fonts.googleapis.com</Allow>
    </Source>
    <Source name="script-src">
        <Allow>'self'</Allow>
        <Allow>'unsafe-inline'</Allow>
        <Allow>code.jquery.com</Allow>
        <Allow>ajax.aspnetcdn.com</Allow>
    </Source>
</Policy>
```

## Questions?

If you have questions, feel free to ask them [here](https://github.com/Offroadcode/umbraco-content-security-policy/issues).

## Contribute

Want to contribute to the Easy Content-Security-Policy package? You'll want to use Grunt (our task runner) to help you integrate with a local copy of Umbraco.

### Install Dependencies
*Requires Node.js to be installed and in your system path*

    npm install -g grunt-cli && npm install -g grunt
    npm install

### Build

    grunt

Builds the project to /dist/. These files can be dropped into an Umbraco 7 site, or you can build directly to a site using:

    grunt --target="D:\inetpub\mysite"

You can also watch for changes using:

    grunt watch
    grunt watch --target="D:\inetpub\mysite"

If you want to build the package file (into a pkg folder), use:

    grunt umbraco
