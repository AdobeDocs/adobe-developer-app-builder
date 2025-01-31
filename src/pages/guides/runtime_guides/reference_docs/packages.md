# Working with Packages

Packages are a simple and yet important concept in I/O Runtime. You can use packages to handle versioning (deploy new version of your actions in a new package), create different actions with the same name within the same namespace, group together actions that are related, share actions with other parties, and much more.

You create a package by running:

```
aio rt:package:create my-fancy-package 
```

Deleting packages works only if they are empty, that is, no actions or triggers have been defined in them:

```
aio rt:package:delete my-fancy-package
```

Packages allow defining default parameters at the package level. All actions defined in that package will then be able to read the default parameters. In this example a param called `name` is set, with the value `some-value`:

```
$ aio rt:package:update my-fancy-package --param name some-value
```

Add this flag `shared`to make a package available to any other user of the system:

```
$ aio rt:package:update my-fancy-package --shared yes
```

To use a shared package, you must first bind it. In this example, we bind `my-fancy-package` to a package called `my-package`. This allows you to invoke any action defined in the original namespace as part of the bound package.

```
$ aio rt:package:bind /namespace-where-package-is-defined/my-fancy-package my-package
```

## Shared packages and permissions

Shared packages enforce `execute-only` permission for any operation is initiated from outside the namespace owning the package. Assuming there is a shared package `my-package` in namespace `a`, and a namespace `b` that uses the shared package, then:

* Invoking `my-package` actions from namespace `b` or `a` will work
* Trying to get, edit, or delete `my-package` code from namespace `b` will fail
* Management of `my-package` -  create, read, update, or delete - will only work from namespace `a`

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).