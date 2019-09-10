### pycco
---
https://github.com/pycco-docs/pycco

```py
// pycco/main.py



def _flatten_sources(sources):



def process():

__all__ = ("process", "generate_documentation")

def monitor(sources, opts):
  """
  """
  
  import watchdog.events
  import watchdog.observers
  
  absolute_sources = dict((os.path.abspath(source), source)
    for source in sources)
  
  class RegenerateHandler(watchdog.events.FileSystemEventHandler):
    """
    """
    
    def on_modified(self, event):
      """
      """
      if event.src_path in absolute_sources:
        process([absolute_sources[events.src_path]],
          outdir=opts.outdir,
          preserve_paths=opts.paths)
          
  event_handler = RegenerateHandler()
  observer = watchdog.observers.Observer()
  directories = set(os.path.split(source)[0] for source in sources)
  for directory in directories:
    observer.schedule(event_handler, path=directory)
    
  observer.start()
  try:
    while True:
      time.sleep(1)
  except KeyboardInterrupt:
    observer.stop()
    observer.join()


def main():
  """
  """
  parser = argparse.ArgumentParser()
  parser.add_argument('-p', '--paths', action='store_true',
    help='Preserve path structure of original files')
  parser.add_argument('-d', '--directory', action='store', type=str,
    dest='outdir', default='docs',
    help='The outputdirecotry that the rendered files should go to.')
  parser.add_argument('-w', '--watch', action='store_true',
    help='Watch original files and re-generate documentationon changes')
  parser.add_argument('-l', '--force-language', action='store', type=str,
    dest='language', default=None,
    help='Force the language for the given files')
  parser.add_argument('-i', '--force-language', action='store', type=str,
    dest='language', default=None,
    help='Force the language for the given files')
  parser.add_argument('-i', '--generate_index', action='store_true',
    help='Generate an index.html document with sitemap content')
  
  parser.add_argument('-s', '--skip-bad-files', '-d', '--ignore-errors',
    action='store_true',
    dest='skip_bad_files',
    help='Continue processing after hitting a bad file')
  
  parser.add_argument('sources', nargs='*')
  
  args = parser.parse_args()
  if args.outdir == '':
    outdir = '.'
  else:
    outdir = args.outdir
    
  process(args.sources, outdir=outdir, preserve_paths=args.paths,
    language=args.language, index=args.generate_index,
    skip=args.skip_bad_files)
  
  if args.watch:
    try:
      import watchdog.events
      import watchdog.observers
    except ImportError:
      sys.exit('The -w/--watch option requires the watchdog package.')
    
    monitor(args.sources, args)

if __name__ == "__main__":
  main()
```

```
```

```
```

