workspace(
    name = "io_kythe_contrib",
    managed_directories = {"@npm": ["node_modules"]},
)

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
load("@bazel_tools//tools/build_defs/repo:utils.bzl", "maybe")

KYTHE_COMMIT = "visibility"

http_archive(
    name = "io_kythe",
    sha256 = "64f3aa5c9928293106143a7da319e7cfc39acab995b7c63f2b27d06580dede97",
    strip_prefix = "kythe-%s" % KYTHE_COMMIT,
    urls = [
        # TODO(johnedmonds): Change to github.com/kythe/kythe when https://github.com/kythe/kythe/pull/5094 is submitted.
        "https://github.com/johnedmonds/kythe/archive/%s.zip" % KYTHE_COMMIT,
    ],
)

load("@io_kythe//:version.bzl", "MAX_VERSION", "MIN_VERSION", "check_version")

# Check that the user has a version between our minimum supported version of
# Bazel and our maximum supported version of Bazel.
check_version(MIN_VERSION, MAX_VERSION)

http_archive(
    name = "bazel_toolchains",
    sha256 = "179ec02f809e86abf56356d8898c8bd74069f1bd7c56044050c2cd3d79d0e024",
    strip_prefix = "bazel-toolchains-4.1.0",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/bazel-toolchains/releases/download/4.1.0/bazel-toolchains-4.1.0.tar.gz",
        "https://github.com/bazelbuild/bazel-toolchains/releases/download/4.1.0/bazel-toolchains-4.1.0.tar.gz",
    ],
)

load("@io_kythe//:setup.bzl", "kythe_rule_repositories")

kythe_rule_repositories()

load("//:setup.bzl", "kythe_contrib_rule_repositories")

kythe_contrib_rule_repositories()

# TODO(schroederc): remove this.  This needs to be loaded before loading the
# go_* rules.  Normally, this is done by go_rules_dependencies in external.bzl,
# but because we want to overload some of those dependencies, we need the go_*
# rules before go_rules_dependencies.  Likewise, we can't precisely control
# when loads occur within a Starlark file so we now need to load this
# manually...
load("@io_bazel_rules_go//go/private:repositories.bzl", "go_name_hack")

maybe(
    go_name_hack,
    name = "io_bazel_rules_go_name_hack",
    is_rules_go = False,
)

# optional: setup ScalaTest toolchain and dependencies
load("@io_bazel_rules_scala//testing:scalatest.bzl", "scalatest_repositories", "scalatest_toolchain")

scalatest_repositories()

scalatest_toolchain()

load("@io_kythe//:external.bzl", "kythe_dependencies")

kythe_dependencies()

load("@io_bazel_rules_scala//:scala_config.bzl", "scala_config")

scala_config(scala_version = "2.12.12")

load("@io_bazel_rules_scala//scala:scala.bzl", "scala_repositories")

scala_repositories()

load("@io_bazel_rules_scala//scala:toolchains.bzl", "scala_register_toolchains")

scala_register_toolchains()

load("@io_kythe//tools/build_rules/external_tools:external_tools_configure.bzl", "external_tools_configure")

external_tools_configure()

load("@npm//@bazel/labs:package.bzl", "npm_bazel_labs_dependencies")

npm_bazel_labs_dependencies()

load("@maven//:compat.bzl", "compat_repositories")

compat_repositories()

# If the configuration here changes, run tools/platforms/configs/rebuild.sh
load("@bazel_toolchains//rules:environments.bzl", "clang_env")
load("@bazel_toolchains//rules:rbe_repo.bzl", "rbe_autoconfig")
load("@io_kythe//tools/platforms:toolchain_config_suite_spec.bzl", "DEFAULT_TOOLCHAIN_CONFIG_SUITE_SPEC")

rbe_autoconfig(
    name = "rbe_default",
    env = clang_env(),
    export_configs = True,
    toolchain_config_suite_spec = DEFAULT_TOOLCHAIN_CONFIG_SUITE_SPEC,
    use_legacy_platform_definition = False,
)

load(
    "@bazelruby_rules_ruby//ruby:defs.bzl",
    "ruby_bundle",
)

ruby_bundle(
    name = "website_bundle",
    bundler_version = "2.1.4",
    gemfile = "//kythe/web/site:Gemfile",
    gemfile_lock = "//kythe/web/site:Gemfile.lock",
)