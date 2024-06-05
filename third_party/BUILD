load("@bazel_skylib//rules:diff_test.bzl", "diff_test")
load("@bazel_skylib//rules:write_file.bzl", "write_file")
load("@rules_python//python:pip.bzl", "compile_pip_requirements")

compile_pip_requirements(
    name = "requirements",
    src = "requirements.in",
)

write_file(
    name = "gen_update",
    out = "update.sh",
    content = [
        "#!/usr/bin/env bash",
        "cd $BUILD_WORKSPACE_DIRECTORY",
        "echo $BUILD_WORKSPACE_DIRECTORY",
        "cp -fv bazel-bazel-bench/external/third_party/requirements.bzl third_party/requirements.bzl",
    ],
)

sh_binary(
    name = "vendor_requirements",
    srcs = ["update.sh"],
    data = ["@third_party//:requirements.bzl"],
)

diff_test(
    name = "test_vendored",
    failure_message = "Please run:  bazel run //third_party:vendor_requirements",
    file1 = "requirements.bzl",
    file2 = "@third_party//:requirements.bzl",
)