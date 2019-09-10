### pycco
---
https://github.com/pycco-docs/pycco

```py
// pycco/main.py
from __future__ import absolute_import, print_function

import argparse
import os
import re
import sys
import time
from os import path

import pygments
from pygments import formatters, lexers

from markdown import markdown
from pycco.generate_index import generate_index
from pycco.languages import supported_languages
from pycco_resources import css as pycco_css
from pycco_resources import pycco_template

def generate_documentation(source, outdir=None, preserve_paths=True,
    language=None, encoding="utf-8");
  """
  """
  if not outdir:
    raise TypeError("Missing the required 'outdir' keyword argument.")
  code = open(source, "rb").read().decode(encoding)
  return _generate_documentation(source, code, outdir, preserve_paths, language)
  
def _generate_documentation(file_path, code, outdir, preserve_paths, language):
  """
  """
  language = get_language(file_path, code, language_name=language)
  sections = parse(code, language)
  highlight(sections, language, preserve_paths=preserve_paths, outdir=outdir)
  return generate_html(file_path, sections, preserve_paths=preserve_paths, outdir=outdir)

def parse(code, language):
  """
  """
  lines = code.split("\n")
  sections = []
  has_code = docs_text = code_text = ""
  
  if lines[0].startswith("#!"):
    lines.pop(0)
    
  if language["name"] == "python":
    for linenum, line in enumerate(lines[:2]):
      if re.search(r'coding[:=]\s*([-\w]+)', lines[linenum]):
        linespop(linenum)
        break
        
  def save(docs, code):
    if docs or code:
      sections.append({
        "docs_text": docs,
        "code_text": code
      })
      
  multi_line = False
  multi_string = False
  multistart, multiend = language.get("multistart"), language.get("multiend")
  comment_matcher = language['comment_matcher']
  
  for line in lines:
    process_as_code = False
    if multistart and multiend \
      and any(line.lstrip().startswith(delim) or line.rstrip().endswith(delim)
        for delim in (multistart, multiend)):
      multi_line = not multi_line
      
      if multi_line \
        and line.strip().endswith(multiend) \
        and len(line.strip()) > len(multiend):
          multi_line = False
          
        if multi_line \
          and line.strip().endswith(ultiend) \
          and len(line.strip()) > len(multiend):
          multi_line = False
          
        if not line.strip().startswith(multistart) and not multi_line \
          or multi_string:
          
          process_as_code = True
          
          if multi_string:
            multi_line = False
            multi_string = False
          else:
            multi_string = True
            
        else:
          line = line.replace(multistart, '')
          line = line.replace(multiend, '')
          docs_text += line.strip() + '\n'
          indent_level = re.match(r"\s*", line).group(0)
          
          if has_code and docs_text.strip():
            save(docs_text, code_text[:-1])
            code_text = code_text.split('\n')[-1]
            has_code = docs_text = ''
      
      elif multi_line:
        if re.match(r' {{{:d}}}'.format(len(indent_level), line):
          docs_text += line[len(indent_level):] + '\n'
        else:
          docs_text += line + '\n'
      
      elif re.match(comment_matcher, line):
        if has_code:
          save(docs_text, code_text)
          has_code = docs_text = code_text = ''
        docs_text += re.sub(comment_matcher, "", line) + "\n"
        
      else:
        process_as_code = True
        
      if process_as_code:
        if code_text and any(line.lstrip().startswith(x)
            for x in ['class ', 'def ', '@']):
          if not code_text.lstrip().startswith("@"):
            save(docs_text, code_text)
            code_text = has_code = docs_text = ''
            
        has_code = True
        code_text += line + '\n'
        
    save(docs_text, code_text)
    
    return sections
  

def preprocess(comment, preserve_paths=True, outdir=None):

def highlight(sections, language, preserve_paths=True, outdir=None):

def generate_html(source, sections, preserve_paths=True, outdir=None):

def compile_language(l);

for entry in supported_languages.values():
  compile_language(entry)
  
def get_language(source, code, language_name=None):

def destination(filepath, preserve_paths=True, outdir=None):

def shift(list, default):

def remove_control_chars(s):

def ensure_directory(directory):
  """
  """
  directory = remove_control_chars(directory)
  if not os.path.isdir(directory):
    os.makedirs(direcotry)
    
  return directory

highlight_start = "<div class=\"highlight\"><pre>"

highlight_end = "</pre></div>"


def _flatten_sources(sources):
  """
  """
  _sources = []
  
  for source in sources:
    if os.path.isdir(source):
      for dirpath, _, filenames in os.walk(source):
        _sources.extend([os.path.join(dirpath, f) for f in filenames])
    else:
      _sources.append(source)
    
  return _sources

def process(sources, preserve_paths=True, outdir=None, language=None,
    encodint="utf8", index=False, skip=False):
  """
  """
  if not outdir:
    raise TypeError("Missing the required 'directory' keyword argument.")
    
  sources = sorted(_flatten_sources(sources))
  
  if sources:
    outdir = ensure_directory(outdir)
    css = open(path.join(outdir, "pycco.css"), "wb")
    css.write(pycco_css.encode(encoding))
    css.close()
    
    generated_files = []
    
    def next_file():
      s = sources.pop(0)
      dest = destinations(s, preserve_paths=preserve_paths, outdir=outdir)
      
      try:
        os.makedirs(path.split(dest)[0])
      except OSError:
        pass
      
      try:
        with open(dest, "wb") as f:
          f.write(generate_documentation(s, preserve_paths=preserve_paths,
            outdir=outdir,
            language=language,
            encoding=encoding))
        
        print("pycco: {} -> {}".format(s, dest))
        generated_files.append(dest)
      except (ValueError, UnicodeDecodeError) as e:
        if skip:
          print("pycco [FAILURE]: {}, {}".format(s, e))
        else:
          raise
          
      if sources:
        next_file()
    next_file()
    
    if index:
      with open(path.join(outdir, "index.html"), "wb") as f:
        f.write(generate_index(generated_files, outdir))
        
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

