# DotNinja

This my answer to the age old question

> "How do I manage my dotfiles if I am anal about not having a git repo in my home directory?"
>
> — Mr. Someone (Perhaps Me)

Ladies and gents, I belive I have an answer. This tool is for the types that
like to have utmost control over organisation of their dotfiles, for those that 
do not accept the flat structure and want to group their files by topics. For those
who need to bootstrap more than just one machine and perhaps more than just one user.

Enter The DOTNinja, a highly configurable dotfile organizer.

All you need to get started is a dir with the following structure:
 
```
(dir)/
 |>(category)/
 |-| dot.conf
 |-|>(topic1)
 |-|-| (..files..)
 |-|-| (..files..)
 |-|>(topic2)
 |-|-| (..files..)
 |-|-| (..files..)
 |-| (..)
```

Example:

```
dotfiles/
 |>common/
 |-| dot.conf
 |-| .DS_Store
 |-|>@misc/
 |-|-| junk1
 |-|-| junk2
 |-|>bin/
 |-|-| my_awesome_tool
 |-|-| .DS_Store
 |-|>vim/
 |-|-| init.vim
 |-|-|>colors/
 |-|-|-| colorscheme.vim
 |-|>tmux/
 |-|-| tmux.conf
```

You might have rightfully noticed that in the prvious example files appear
without dots. That `tmux.conf` is located in a tmux dir while it should be in
the root of home directory, while `vim` folder should be in the root directory
as opposed to its contents. And that is the magic, you get to chose how
DOTNinja symlinks your topics into the home dir.

And you do that using a configuration `dot.conf` on per category basis:

Here is an example:

```perl
package Dot::Conf;

our $config = {
  # Defines the path that will be used as reference
  # for all relative paths
  'root' => '~',

  # Available dir modes:
  #   id   -- means the folder istself will end up in root
  #   root -- means the content of folder will end up in root
  #   (can be configured per folder in 'dirconf')
  'default_dir_mode' => 'root',

  # Symlink extesion for symlink_rec mode:
  #   If any folder ends in this extension it will stop recursion
  #   and symlink itself into destination
  #   (can be configured per folder in 'dirconf')
  'default_symlink_ext' => 'sym',

  # Available copy modes:
  #   copy        -- will copy the files and dirs
  #   symlink     -- will symlink file or dir directly
  #   symlink_rec -- will symlink files into place recursively
  #   (can be configured per folder in 'dirconf')
  'default_copy_mode' => 'symlink_rec',

  # Dot modes:
  #   top_level -- will prepend only to topmost level in this folder
  #   no_dot    -- will not prepend dots to any files (usefull for bin for ex.)
  #   (can be configured per folder in 'dirconf')
  'default_dot_mode' => 'top_level',

  # Folders listed in here will not be acted upon
  'disabled' => [ '@misc' ],

  # Per directory configurations:
  #   Top level key is the name of the directory.
  #   Available keys are the following:
  #     dir_mode    -- see documentation for default_dir_mode 
  #     name        -- when the dir_mode is set to 'id' and copy mode is not 'symlink_rec'
  #                    you can use this to override the name of the dir your are linking/copying
  #     copy_mode   -- see documentation for default_copy_mode 
  #     dot_mode    -- see documentation for default_dot_mode
  #     symlink_ext -- see documentation for default_symlink_ext
  #     root        -- overrides dir mode and puts the contents in that place
  'dirconf' => {
    'bin'  => { 'dot_mode'  => 'no_dot', 'copy_mode' => 'copy' } ,
    'tmux' => { 'dir_mode'  => 'root'                          } ,
  },

  # Files listed here will be ignored
  #   The patterns will be matched and globbed
  #   This works roughly like .gitignore
  'ignore' => ['.DS_Store', '*.bak'],
};
```
