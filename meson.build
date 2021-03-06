project('ml-api', 'c', 'cpp',
  version: '1.7.2',
  license: ['Apache-2.0'],
  meson_version: '>=0.50.0',
  default_options: [
    'werror=true',
    'warning_level=1',
    'c_std=gnu89',
    'cpp_std=c++14'
  ]
)

cc = meson.get_compiler('c')
cxx = meson.get_compiler('cpp')

# Dependencies
glib_dep = dependency('glib-2.0')
gmodule_dep = dependency('gmodule-2.0')
gst_dep = dependency('gstreamer-1.0')
gst_app_dep = dependency('gstreamer-app-1.0')
nnstreamer_dep = dependency('nnstreamer')

# Set version info
api_version = meson.project_version()
api_version_split = api_version.split('.')

add_project_arguments('-DVERSION="' + api_version + '"', language: ['c', 'cpp'])
add_project_arguments('-DVERSION_MAJOR="' + api_version_split[0] + '"', language: ['c', 'cpp'])
add_project_arguments('-DVERSION_MINOR="' + api_version_split[1] + '"', language: ['c', 'cpp'])
add_project_arguments('-DVERSION_MICRO="' + api_version_split[2] + '"', language: ['c', 'cpp'])

# Define warning flags for c and cpp
warning_flags = [
  '-Wwrite-strings',
  '-Wformat',
  '-Wformat-nonliteral',
  '-Wformat-security',
  '-Winit-self',
  '-Waddress',
  '-Wno-multichar',
  '-Wvla',
  '-Wpointer-arith'
]

warning_c_flags = [
  '-Wmissing-declarations',
  '-Wmissing-include-dirs',
  '-Wmissing-prototypes',
  '-Wnested-externs',
  '-Waggregate-return',
  '-Wold-style-definition',
  '-Wdeclaration-after-statement'
]

# Setup warning flags for c and cpp
foreach extra_arg : warning_flags
  if cc.has_argument (extra_arg)
    add_project_arguments([extra_arg], language: 'c')
  endif
  if cxx.has_argument (extra_arg)
    add_project_arguments([extra_arg], language: 'cpp')
  endif
endforeach

foreach extra_arg : warning_c_flags
  if cc.has_argument (extra_arg)
    add_project_arguments([extra_arg], language: 'c')
  endif
endforeach

# Set project args
if get_option('enable-tizen')
  # Pass __TIZEN__ to the compiler
  add_project_arguments('-D__TIZEN__=1', language: ['c', 'cpp'])

  tizenVmajor = get_option('tizen-version-major')
  add_project_arguments('-DTIZENVERSION='+tizenVmajor.to_string(), language: ['c', 'cpp'])

  if get_option('enable-tizen-feature-check')
    add_project_arguments('-D__FEATURE_CHECK_SUPPORT__', language: ['c', 'cpp'])
  endif

  if get_option('enable-tizen-privilege-check')
    add_project_arguments('-D__PRIVILEGE_CHECK_SUPPORT__', language: ['c', 'cpp'])
  endif
endif

# Check neural network framework
# TODO: add frameworks required to build and run test
# tendorflow
tf_dep = dependency('tensorflow', required: false)
if tf_dep.found()
  add_project_arguments('-DENABLE_TENSORFLOW=1', language: ['c', 'cpp'])
endif

# tendorflow-lite
tf1lite_dep = dependency('tensorflow-lite', required: false)
if tf1lite_dep.found()
  add_project_arguments('-DENABLE_TENSORFLOW_LITE=1', language: ['c', 'cpp'])
endif

tf2lite_dep = dependency('tensorflow2-lite', required: false)
if tf2lite_dep.found()
  add_project_arguments('-DENABLE_TENSORFLOW2_LITE=1', language: ['c', 'cpp'])
endif

# NNFW (ONE)
nnfw_dep = dependency('nnfw', required: false)
if not nnfw_dep.found()
  nnfw_dep = cc.find_library('nnfw-dev', required: false)
endif
if nnfw_dep.found()
  add_project_arguments('-DENABLE_NNFW_RUNTIME=1', language: ['c', 'cpp'])
endif

# ArmNN
armnn_dep = dependency('armnn', required: false)
if armnn_dep.found()
  add_project_arguments('-DENABLE_ARMNN=1', language: ['c', 'cpp'])
endif

# Set install path
api_install_prefix = get_option('prefix')
api_install_libdir = join_paths(api_install_prefix, get_option('libdir'))
api_install_bindir = join_paths(api_install_prefix, get_option('bindir'))
api_install_includedir = join_paths(api_install_prefix, get_option('includedir'))
api_install_inidir = get_option('sysconfdir')

# Set default configuration
api_conf = configuration_data()
api_conf.set('VERSION', api_version)
api_conf.set('PREFIX', api_install_prefix)
api_conf.set('EXEC_PREFIX', api_install_bindir)
api_conf.set('LIB_INSTALL_DIR', api_install_libdir)
api_conf.set('INCLUDE_INSTALL_DIR', api_install_includedir)

# Build C-API
subdir('c')

# Run unittest
if get_option('enable-test')
  subdir('tests')
endif
