# Copyright 2018 The JAX Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# JAX is Autograd and XLA

load(
    "//jaxlib:jax.bzl",
    "nanobind_extension",
    "py_library_providing_imports_info",
    "pytype_library",
)
load("//jaxlib:symlink_files.bzl", "symlink_files")

licenses(["notice"])

package(
    default_applicable_licenses = [],
    default_visibility = ["//jax:internal"],
)

# This makes xla_extension module accessible from jax._src.lib.
genrule(
    name = "xla_extension_py",
    outs = ["xla_extension.py"],
    cmd = "echo 'from xla.xla.python.xla_extension import *\n' > $@",
)

py_library_providing_imports_info(
    name = "jaxlib",
    srcs = [
        "gpu_common_utils.py",
        "gpu_linalg.py",
        "gpu_prng.py",
        "gpu_rnn.py",
        "gpu_solver.py",
        "gpu_sparse.py",
        "gpu_triton.py",
        "hlo_helpers.py",
        "init.py",
        "lapack.py",
        "plugin_support.py",
        ":version",
        ":xla_client",
        ":xla_extension_py",
    ],
    data = [":ffi_headers"],
    lib_rule = pytype_library,
    deps = [
        ":cpu_feature_guard",
        ":utils",
        "//jaxlib/cpu:_lapack",
        "//jaxlib/mlir",
        "//jaxlib/mlir:arithmetic_dialect",
        "//jaxlib/mlir:builtin_dialect",
        "//jaxlib/mlir:chlo_dialect",
        "//jaxlib/mlir:func_dialect",
        "//jaxlib/mlir:gpu_dialect",
        "//jaxlib/mlir:ir",
        "//jaxlib/mlir:llvm_dialect",
        "//jaxlib/mlir:math_dialect",
        "//jaxlib/mlir:memref_dialect",
        "//jaxlib/mlir:mhlo_dialect",
        "//jaxlib/mlir:nvgpu_dialect",
        "//jaxlib/mlir:nvvm_dialect",
        "//jaxlib/mlir:pass_manager",
        "//jaxlib/mlir:scf_dialect",
        "//jaxlib/mlir:sdy_dialect",
        "//jaxlib/mlir:sparse_tensor_dialect",
        "//jaxlib/mlir:stablehlo_dialect",
        "//jaxlib/mlir:vector_dialect",
        "//jaxlib/mosaic",
        "//jaxlib/triton",
        "@xla//xla/python:xla_extension",
    ],
)

symlink_files(
    name = "version",
    srcs = ["//jax:version.py"],
    dst = ".",
    flatten = True,
)

symlink_files(
    name = "xla_client",
    srcs = ["@xla//xla/python:xla_client"],
    dst = ".",
    flatten = True,
)

symlink_files(
    name = "ffi_headers",
    srcs = ["@xla//xla/ffi/api:all_headers"],
    dst = "include/xla/ffi/api",
    flatten = True,
)

exports_files([
    "README.md",
    "setup.py",
])

cc_library(
    name = "absl_status_casters",
    hdrs = ["absl_status_casters.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
    ],
)

cc_library(
    name = "ffi_helpers",
    hdrs = ["ffi_helpers.h"],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:str_format",
        "@xla//xla/ffi/api:c_api",
        "@xla//xla/ffi/api:ffi",
    ],
)

cc_library(
    name = "kernel_nanobind_helpers",
    hdrs = ["kernel_nanobind_helpers.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":kernel_helpers",
        "@com_google_absl//absl/base",
        "@nanobind",
        "@xla//xla/ffi/api:c_api",
        "@xla//xla/tsl/python/lib/core:numpy",
    ],
)

cc_library(
    name = "kernel_helpers",
    hdrs = ["kernel_helpers.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base",
        "@com_google_absl//absl/status:statusor",
    ],
)

cc_library(
    name = "pass_boilerplate",
    hdrs = ["pass_boilerplate.h"],
    # compatible with libtpu
    deps = [
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:Support",
    ],
)

cc_library(
    name = "handle_pool",
    hdrs = ["handle_pool.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/synchronization",
    ],
)

# This isn't a CPU kernel. This exists to catch cases where jaxlib is built for the wrong
# target architecture.
nanobind_extension(
    name = "cpu_feature_guard",
    srcs = ["cpu_feature_guard.c"],
    module_name = "cpu_feature_guard",
    deps = [
        "@xla//third_party/python_runtime:headers",
    ],
)

nanobind_extension(
    name = "utils",
    srcs = ["utils.cc"],
    module_name = "utils",
    deps = [
        "@com_google_absl//absl/cleanup",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/synchronization",
        "@nanobind",
        "@xla//third_party/python_runtime:headers",
    ],
)

cc_library(
    name = "gpu_plugin_extension",
    srcs = ["gpu_plugin_extension.cc"],
    hdrs = ["gpu_plugin_extension.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":kernel_nanobind_helpers",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/strings:string_view",
        "@nanobind",
        "@xla//xla:util",
        "@xla//xla/ffi/api:c_api",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/pjrt/c:pjrt_c_api_ffi_extension_hdrs",
        "@xla//xla/pjrt/c:pjrt_c_api_gpu_extension_hdrs",
        "@xla//xla/pjrt/c:pjrt_c_api_hdrs",
        "@xla//xla/pjrt/c:pjrt_c_api_helpers",
        "@xla//xla/pjrt/c:pjrt_c_api_triton_extension_hdrs",
        "@xla//xla/python:py_client_gpu",
        "@xla//xla/tsl/python/lib/core:numpy",
    ],
)

nanobind_extension(
    name = "cuda_plugin_extension",
    srcs = ["cuda_plugin_extension.cc"],
    module_name = "cuda_plugin_extension",
    deps = [
        ":gpu_plugin_extension",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@local_config_cuda//cuda:cuda_headers",
        "@nanobind",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/tsl/cuda:cublas",
        "@xla//xla/tsl/cuda:cudart",
    ],
)

nanobind_extension(
    name = "rocm_plugin_extension",
    srcs = ["rocm_plugin_extension.cc"],
    module_name = "rocm_plugin_extension",
    deps = [
        ":gpu_plugin_extension",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/strings",
        "@local_config_rocm//rocm:hip",
        "@local_config_rocm//rocm:rocm_headers",
        "@nanobind",
    ],
)

# CPU kernels

# TODO(phawkins): Remove this forwarding target.
cc_library(
    name = "cpu_kernels",
    visibility = ["//visibility:public"],
    deps = [
        "//jaxlib/cpu:cpu_kernels",
    ],
    alwayslink = 1,
)

# TODO(phawkins): Remove this forwarding target.
cc_library(
    name = "gpu_kernels",
    visibility = ["//visibility:public"],
    deps = [
        "//jaxlib/cuda:cuda_gpu_kernels",
    ],
    alwayslink = 1,
)
