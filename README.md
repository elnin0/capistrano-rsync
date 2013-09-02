Capistrano::Rsync for Capistrano v3
===================================
[![Gem version](https://badge.fury.io/rb/capistrano-rsync.png)](http://badge.fury.io/rb/capistrano-rsync)

**Deploy with Rsync** to your server from any local (or remote) repository when using [**Capistrano**](http://www.capistranorb.com/).  
Saves you from having to install Git on your production machine and allows you to customize which files you want to deploy. Allows also you to easily precompile things on your local machine before deploying.

### Tour
- Works with the new [**Capistrano v3**](http://www.capistranorb.com/) ([source code](https://github.com/capistrano/capistrano)) versions `>= 3.0.0pre14` and `< 4`.
- Suitable for deploying any apps, be it Ruby, Rails, Node.js or others.  
- Exclude files from being deployed with Rsync's `--exclude` options.
- Precompile files or assets easily before deploying, like JavaScript or CSS.
- Caches your previously deployed code to speed up deployments ~1337%.
- Currently works only with Git (as does Capistrano v3), so please shout out your interest in other SCMs.


Using
-----
Install with:
```
gem install capistrano-rsync
```

Require it at the top of your `Capfile` or `config/deploy.rb`:
```ruby
require "capistrano/rsync"
```

Set some `rsync_options` to your liking:
```ruby
set :rsync_options, %w[--recursive --delete --delete-excluded --exclude .git*]
```

And after setting regular Capistrano options, deploy as usual!
```
cap deploy
```

### Implementation
Capistrano::Rsync clones your repository to `tmp/cache` on your local machine, checks out the branch set in the `branch` variable (`master` by default) and then Rsyncs that directory to your servers.

### Exclude files from being deployed
If you don't want to deploy everything you've committed to your repository, pass some `--exclude` options to Rsync:
```ruby
set :rsync_options, %w[
  --recursive --delete --delete-excluded
  --exclude .git*
  --exclude /config/database.yml
  --exclude /test/***
]
```

### Precompile assets before deploy
Capistrano::Rsync runs `rsync:stage` before rsyncing. Hook to that like this:
```ruby
task :precompile do
  Dir.chdir fetch(:rsync_stage) do
    system "rake", "assets:precompile"
  end
end

after "rsync:stage", "precompile"
```

### Deploy release without symlinking the current directory
```
cap rsync:release
```


Configuration
-------------
Set Capistrano variables with `set name, value`.

Name          | Default | Description
--------------|---------|------------
branch        | `master` | The Git branch to checkout.  
repo_url      | `.` | The path or URL to a Git repository to clone from.  
rsync_stage   | `tmp/deploy` | Path where to clone your repository for staging, checkouting and rsyncing. Can be both relative or absolute.
rsync_cache   | `shared/deploy` | Path where to cache your repository on the server to avoid rsyncing from scratch each time. Can be both relative or absolute.<br> Set to `nil` if you want to disable the cache.
rsync_options | `[]` | Array of options to pass to `rsync`.  


License
-------
Capistrano::Rsync is released under a *Lesser GNU Affero General Public License*, which in summary means:

- You **can** use this program for **no cost**.
- You **can** use this program for **both personal and commercial reasons**.
- You **do not have to share your own program's code** which uses this program.
- You **have to share modifications** (e.g bug-fixes) you've made to this program.

For more convoluted language, see the `LICENSE` file.


About
-----
**[Andri Möll](http://themoll.com)** made this happen.  
[Monday Calendar](https://mondayapp.com) was the reason I needed this.

If you find Capistrano::Rsync needs improving, please don't hesitate to type to me now at [andri@dot.ee](mailto:andri@dot.ee) or [create an issue online](https://github.com/moll/capistrano-rsync/issues).
