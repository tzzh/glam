# CPM

CPM offers a flexible way to bring binaries into scope for a shell, piggybacking
on [tools deps](https://clojure.org/guides/deps_and_cli) (this may change to a
standalone solution in the future, based on a GraalVM binary).

Work in progress, not sure where this is going, not ready for production,
breaking changes will happen.

## Usage

Place `<package-org>/<package-name>.cpm.edn` in your Clojure dependency.

E.g. in the CPM repo's `test-resources` directory, there is `org.babashka/babashka.cpm.edn`:

``` clojure
{:package/name org.babashka/babashka
 :package/description ""
 :package/version "0.2.1"
 :package/license ""
 :package/artifacts
 [{:os/name "Mac.*"
   :os/arch "x86_64"
   :artifact/url "https://github.com/borkdude/babashka/releases/download/v0.2.1/babashka-0.2.1-macos-amd64.zip"
   :artifact/executables ["bb"]}
  {:os/name "Linux.*"
   :os/arch "amd64"
   :artifact/url "https://github.com/borkdude/babashka/releases/download/v0.2.1/babashka-0.2.1-linux-amd64.zip"
   :artifact/executables ["bb"]}
  {:os/name "Windows.*"
   :os/arch "amd64"
   :artifact/url "https://github.com/borkdude/babashka/releases/download/v0.2.1/babashka-0.2.1-windows-amd64.zip"
   :artifact/executables ["bb.exe"]}]}
```

To create a path with packages:

``` clojure
$ clojure -M:test -m cpm.main --install clj-kondo/clj-kondo org.babashka/babashka
/Users/borkdude/.cpm/packages/org.babashka/babashka/0.2.1:/Users/borkdude/.cpm/packages/clj-kondo/clj-kondo/2020.09.09
```

Use `--verbose` for more output, `--force` for re-downloading packages.

The resulting path can then be used to add programs on the path for the current shell:

``` clojure
$ export PATH=$(clojure -M:test -m cpm.main --install clj-kondo/clj-kondo org.babashka/babashka):$PATH
$ which bb
/Users/borkdude/.cpm/packages/org.babashka/babashka/0.2.1/bb
$ which clj-kondo
/Users/borkdude/.cpm/packages/clj-kondo/clj-kondo/2020.09.09/clj-kondo
$ bb '(+ 1 2 3)'
6
```

CPM can also run with [babashka](https://github.com/borkdude/babashka) for fast startup (requires version from master, check CircleCI artifacts):

``` clojure
$ bb -cp src:test-resources -m cpm.main --install clj-kondo/clj-kondo
/Users/borkdude/.cpm/packages/clj-kondo/clj-kondo/2020.09.09
export PATH=$(bb -cp src:test-resources -m cpm.main clj-kondo):$PATH
$ which clj-kondo
/Users/borkdude/.cpm/packages/clj-kondo/clj-kondo/2020.09.09/clj-kondo
```

### Global

To install packages globally, use `--global`. This writes a path of globally installed packages to `$HOME/.cpm/path`:

``` clojure
$ bb -cp src:test-resources -m cpm.main --install clj-kondo/clj-kondo --global --verbose
Package clj-kondo/clj-kondo already installed
Wrote /Users/borkdude/.cpm/path
/Users/borkdude/.cpm/repository/clj-kondo/clj-kondo/2020.09.09
```

Add this path to `$PATH` in your favorite `.bashrc` analog:

``` clojure
# cpm
export PATH="`cat $HOME/.cpm/path 2>/dev/null`:$PATH"
```

## License

Copyright © 2019-2020 Michiel Borkent

Distributed under the EPL License. See LICENSE.
