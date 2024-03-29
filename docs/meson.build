version_xml = configuration_data()
version_xml.set('GDK_PIXBUF_VERSION', meson.project_version())
configure_file(input: 'version.xml.in',
               output: 'version.xml',
               configuration: version_xml)

if gobject_dep.type_name() == 'pkgconfig'
  glib_prefix = gobject_dep.get_pkgconfig_variable('prefix')
else
  glib_prefix = get_option('prefix')
endif
glib_docpath = join_paths(glib_prefix, 'share', 'gtk-doc', 'html')
docpath = join_paths(gdk_pixbuf_datadir, 'gtk-doc', 'html')

private_headers = [
  'pixops',
  'gdk-pixbuf.h',
  'gdk-pixbuf-alias.h',
  'gdk-pixbuf-autocleanups.h',
  'gdk-pixbuf-buffer-queue-private.h',
  'gdk-pixbuf-marshal.h',
  'gdk-pixbuf-private.h',
  'gdk-pixbuf-scaled-anim.h',
  'gdk-pixbuf-xlib-private.h',
  'io-ani-animation.h',
  'io-gdip-animation.h',
  'io-gdip-native.h',
  'io-gdip-propertytags.h',
  'io-gdip-utils.h',
  'io-gif-animation.h',
  'xpm-color-table.h',
  'test-images.h',
  'lzw.h',
]

if not get_option('x11')
  private_headers += 'contrib'
endif

if get_option('docs')
  gnome.gtkdoc('gdk-pixbuf',
               main_xml: 'gdk-pixbuf.xml',
               src_dir: [
                 gdk_pixbuf_inc,
                 gdkpixbuf_xlib_inc,
               ],
               dependencies: gdkpixbuf_dep,
               gobject_typesfile: 'gdk-pixbuf.types',
               scan_args: [
                 '--rebuild-types',
                 '--deprecated-guards="GDK_PIXBUF_ENABLE_BROKEN|GDK_PIXBUF_DISABLE_DEPRECATED"',
                 '--ignore-headers=' + ' '.join(private_headers),
               ],
               fixxref_args: [
                 '--html-dir=@0@'.format(docpath),
                 '--extra-dir=@0@'.format(join_paths(glib_docpath, 'glib')),
                 '--extra-dir=@0@'.format(join_paths(glib_docpath, 'gobject')),
                 '--extra-dir=@0@'.format(join_paths(glib_docpath, 'gio')),
               ],
               html_assets: [
                 'composite.png',
               ],
               content_files: [
                 'gdk-pixbuf-from-drawables.xml',
                 'gdk-pixbuf-rendering.xml',
                 'gdk-pixbuf.xml',
                 'gdk-pixbuf-csource.xml',
                 'gdk-pixbuf-query-loaders.xml',
               ],
               install: true)
endif

xsltproc = find_program('xsltproc', required: false)
if get_option('man') and xsltproc.found()
  xlstproc_flags = [
    '--nonet',
    '--stringparam', 'man.output.quietly', '1',
    '--stringparam', 'funcsynopsis.style', 'ansi',
    '--stringparam', 'man.th.extra1.suppress', '1',
    '--stringparam', 'man.authors.section.enabled', '0',
    '--stringparam', 'man.copyright.section.enabled', '0',
  ]

  man_files = [
    'gdk-pixbuf-csource',
    'gdk-pixbuf-query-loaders',
  ]

  foreach m: man_files
    custom_target(m + '-man',
                  input: '@0@.xml'.format(m),
                  output: '@0@.1'.format(m),
                  command: [
                    xsltproc,
                    xlstproc_flags,
                    '-o', '@OUTPUT@',
                    'http://docbook.sourceforge.net/release/xsl/current/manpages/docbook.xsl',
                    '@INPUT@',
                  ],
                  install: true,
                  install_dir: join_paths(gdk_pixbuf_mandir, 'man1'))
  endforeach
endif
