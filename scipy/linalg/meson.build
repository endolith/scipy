# TODO: 64-bit BLAS and LAPACK

__init__py = custom_target('__init__py',
  output: '__init__.py',
  input: '__init__.py',
  command: [copier, '@INPUT@', '@OUTDIR@']
)

cython_linalg = custom_target('cython_linalg',
  output: [
    'cython_blas.pyx',
    'cython_lapack.pyx',
    'cython_blas.pxd',
    'cython_lapack.pxd',
    '_blas_subroutines.h',
    '_lapack_subroutines.h',
    '_blas_subroutine_wrappers.f',
    '_lapack_subroutine_wrappers.f'
  ],
  input: '_generate_pyx.py',
  command: [py3, '@INPUT@', '-o', '@OUTDIR@'],
  # FIXME - we only want to install the .pxd files! See comments for
  #         `pxd_files` further down.
  install: true,
  install_dir: py3.get_install_dir() + 'scipy/linalg'
)

cython_blas_pxd = declare_dependency(sources: cython_linalg[2])

# fblas
fblas_module = custom_target('fblas_module',
  output: ['_fblasmodule.c', '_fblas-f2pywrappers.f'],
  input: 'fblas.pyf.src',
  command: [py3, generate_f2pymod, '@INPUT@', '-o', '@OUTDIR@']
)

py3.extension_module('_fblas',
  [fblas_module, fortranobject_c, g77_abi_wrappers],
  c_args: numpy_nodepr_api,
  include_directories: [inc_np, inc_f2py],
  dependencies: [py3_dep, blas],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

# flapack
deprecated_lapack_routines = [
  'src/lapack_deprecations/cgegv.f',
  'src/lapack_deprecations/dgegv.f',
  'src/lapack_deprecations/sgegv.f',
  'src/lapack_deprecations/zgegv.f'
]

flapack_module = custom_target('flapack_module',
  output: ['_flapackmodule.c', '_flapack-f2pywrappers.f'],
  input: 'flapack.pyf.src',
  command: [py3, generate_f2pymod, '@INPUT@', '-o', '@OUTDIR@']
)

py3.extension_module('_flapack',
  [
    flapack_module,
    fortranobject_c,
    g77_abi_wrappers,
    deprecated_lapack_routines
  ],
  c_args: numpy_nodepr_api,
  include_directories: [inc_np, inc_f2py],
  dependencies: [py3_dep, lapack],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

# TODO: cblas/clapack are built *only* for ATLAS. Why? Is it still needed?

# _flinalg
flinalg_module = custom_target('flinalg_module',
  output: '_flinalgmodule.c',
  input: ['src/flinalg.pyf'],
  command: [py3, generate_f2pymod, '@INPUT@', '-o', '@OUTDIR@']
)

py3.extension_module('_flinalg',
  ['src/det.f', 'src/lu.f', flinalg_module, fortranobject_c],
  c_args: numpy_nodepr_api,
  include_directories: [inc_np, inc_f2py],
  dependencies: [py3_dep, lapack],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

# _interpolative
interpolative_module = custom_target('interpolative_module',
  output: '_interpolativemodule.c',
  input: 'interpolative.pyf',
  command: [py3, generate_f2pymod, '@INPUT@', '-o', '@OUTDIR@']
)

py3.extension_module('_interpolative',
  [
    'src/id_dist/src/dfft.f',
    'src/id_dist/src/id_rand.f',
    'src/id_dist/src/id_rtrans.f',
    'src/id_dist/src/idd_frm.f',
    'src/id_dist/src/idd_house.f',
    'src/id_dist/src/idd_id.f',
    'src/id_dist/src/idd_id2svd.f',
    'src/id_dist/src/idd_qrpiv.f',
    'src/id_dist/src/idd_sfft.f',
    'src/id_dist/src/idd_snorm.f',
    'src/id_dist/src/idd_svd.f',
    'src/id_dist/src/iddp_aid.f',
    'src/id_dist/src/iddp_asvd.f',
    'src/id_dist/src/iddp_rid.f',
    'src/id_dist/src/iddp_rsvd.f',
    'src/id_dist/src/iddr_aid.f',
    'src/id_dist/src/iddr_asvd.f',
    'src/id_dist/src/iddr_rid.f',
    'src/id_dist/src/iddr_rsvd.f',
    'src/id_dist/src/idz_frm.f',
    'src/id_dist/src/idz_house.f',
    'src/id_dist/src/idz_id.f',
    'src/id_dist/src/idz_id2svd.f',
    'src/id_dist/src/idz_qrpiv.f',
    'src/id_dist/src/idz_sfft.f',
    'src/id_dist/src/idz_snorm.f',
    'src/id_dist/src/idz_svd.f',
    'src/id_dist/src/idzp_aid.f',
    'src/id_dist/src/idzp_asvd.f',
    'src/id_dist/src/idzp_rid.f',
    'src/id_dist/src/idzp_rsvd.f',
    'src/id_dist/src/idzr_aid.f',
    'src/id_dist/src/idzr_asvd.f',
    'src/id_dist/src/idzr_rid.f',
    'src/id_dist/src/idzr_rsvd.f',
    'src/id_dist/src/prini.f',
    interpolative_module,
    fortranobject_c
  ],
  c_args: numpy_nodepr_api,
  fortran_args: [
    '-Wno-tabs', '-Wno-conversion', '-Wno-argument-mismatch',
    '-Wno-unused-dummy-argument', '-Wno-maybe-uninitialized'
  ],
  # TODO: add -fallow-argument-mismatch for gfortran >= 10, see gh-11842
  include_directories: [inc_np, inc_f2py],
  dependencies: [py3_dep, lapack],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

# _solve_toeplitz
py3.extension_module('_solve_toeplitz',
  '_solve_toeplitz.pyx',
  c_args: numpy_nodepr_api,
  include_directories: inc_np,
  dependencies: py3_dep,
  install: true,
  subdir: 'scipy/linalg'
)

# _matfuncs_sqrtm_triu:
py3.extension_module('_matfuncs_sqrtm_triu',
  '_matfuncs_sqrtm_triu.pyx',
  c_args: numpy_nodepr_api,
  include_directories: inc_np,
  dependencies: py3_dep,
  install: true,
  subdir: 'scipy/linalg'
)

fwrappers = static_library('fwrappers',
  [
    cython_linalg[4],
    cython_linalg[5],
    cython_linalg[6],
    cython_linalg[7],
    g77_abi_wrappers
  ]
)

cython_blas = py3.extension_module('cython_blas',
  [
    cython_linalg[0],  # cython_blas.pyx
    cython_linalg[2],  # cython_blas.pxd
    cython_linalg[4],  # _blas_subroutines.h
    'fortran_defs.h'
  ],
  link_with: fwrappers,
  include_directories: inc_np,
  dependencies: [py3_dep, blas],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

cython_lapack = py3.extension_module('cython_lapack',
  [
    cython_linalg[1],  # cython_lapack.pyx
    cython_linalg[3],  # cython_lapack.pxd
    cython_linalg[5],  # _lapack_subroutines.h
    'fortran_defs.h'
  ],
  link_with: fwrappers,
  include_directories: inc_np,
  dependencies: [py3_dep, blas],
  install: true,
  link_language: 'fortran',
  subdir: 'scipy/linalg'
)

_decomp_update_pyx = custom_target('_decomp_update',
  output: '_decomp_update.pyx',
  input: '_decomp_update.pyx.in',
  command: [py3, tempita, '@INPUT@', '-o', '@OUTDIR@']
)

py3.extension_module('_decomp_update',
  [
    _decomp_update_pyx,
    _cython_tree, __init__py,
    cython_linalg[3]
  ],
  include_directories: inc_np,
  dependencies: py3_dep,
  install: true,
  subdir: 'scipy/linalg'
)

_cythonized_array_utils = py3.extension_module('_cythonized_array_utils',
  ['_cythonized_array_utils.pyx'],
  c_args: numpy_nodepr_api,
  dependencies: [py3_dep],
  include_directories: [inc_np],
  install: true,
  subdir: 'scipy/linalg'
)

python_sources = [
  '__init__.py',
  '_basic.py',
  '_decomp.py',
  '_decomp_cholesky.py',
  '_decomp_cossin.py',
  '_decomp_ldl.py',
  '_decomp_lu.py',
  '_decomp_polar.py',
  '_decomp_qr.py',
  '_decomp_qz.py',
  '_decomp_schur.py',
  '_decomp_svd.py',
  '_expm_frechet.py',
  '_flinalg_py.py',
  '_interpolative_backend.py',
  '_matfuncs.py',
  '_matfuncs_inv_ssq.py',
  '_matfuncs_sqrtm.py',
  '_misc.py',
  '_procrustes.py',
  '_sketches.py',
  '_solvers.py',
  '_special_matrices.py',
  '_testutils.py',
  'basic.py',
  'blas.py',
  'decomp.py',
  'decomp_cholesky.py',
  'decomp_lu.py',
  'decomp_qr.py',
  'decomp_schur.py',
  'decomp_svd.py',
  'flinalg.py',
  'interpolative.py',
  'lapack.py',
  'matfuncs.py',
  'misc.py',
  'special_matrices.py'
]

py3.install_sources(
  python_sources,
  pure: false,
  subdir: 'scipy/linalg'
)

py3.install_sources(
  ['_cythonized_array_utils.pyi'],
  pure: false,
  subdir: 'scipy/linalg'
)

# TODO: install (only) these pxd files. Neither py3.install_sources nor
#       install_data accept generated data files. What
#       https://mesonbuild.com/Installing.html says is for build targets to
#       use:
#         `custom_target(..., install: true, install_dir: ...)
#         # should use `py3.get_install_dir() + 'scipy/linalg'` ?
#       see https://github.com/mesonbuild/meson/issues/3206
#pxd_files = [
#  cython_linalg[2],  # cython_blas.pxd
#  cython_linalg[3],  # cython_lapack.pxd
#]
#custom_target('cython_blas_lapack_pxd',
#  input : pxd_files,
#  output : ['cython_blas2.pxd', 'cython_lapack2.pxd'],
#  command : ['cp', '@INPUT0@', '@OUTPUT0@', '&&', 'cp', '@INPUT1@', '@OUTPUT1@'],
#  install : true,
#  install_dir: py3.get_install_dir() + 'scipy/linalg'
#)

subdir('tests')