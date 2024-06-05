load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "rules_python",
    sha256 = "4912ced70dc1a2a8e4b86cec233b192ca053e82bc72d877b98e126156e8f228d",
    strip_prefix = "rules_python-0.32.2",
    url = "https://github.com/bazelbuild/rules_python/releases/download/0.32.2/rules_python-0.32.2.tar.gz",
)

load("@rules_python//python:repositories.bzl", "py_repositories")

py_repositories()

load("@rules_python//python:pip.bzl", "pip_parse")
load("@rules_python//python:repositories.bzl", "python_register_toolchains")


# Use a hermetic Python interpreter so that builds are reproducible
# irrespective of the Python version available on the host machine.
python_register_toolchains(
    name = "python3_10",
    python_version = "3.10",
)

load("@python3_10//:defs.bzl", "interpreter")

# This repository isn't referenced, except by our test that asserts the requirements.bzl is updated.
# It also wouldn't be needed by users of this ruleset.
# If you're using envsubst with extra_pip_args, as we do below, the value of the environment
# variables at the time we generate requirements.bzl don't make it into the file, as you may
# verify by inspection; the environment variables at a later time, when we download the
# packages, will be the ones that take effect.
pip_parse(
    name = "third_party",
    envsubst = ["PIP_RETRIES"],
    extra_pip_args = ["--retries=${PIP_RETRIES:-5}"],
    python_interpreter_target = interpreter,
    requirements_lock = "//third_party:requirements.txt",
)

# This example vendors the file produced by `pip_parse` above into the repo.
# This way our Bazel doesn't eagerly fetch and install the pip_parse'd
# repository for builds that don't need it.
# See discussion of the trade-offs in the pip_parse documentation
# and the "vendor_requirements" target in the BUILD file.
load("//third_party:requirements.bzl", "install_deps")

install_deps()