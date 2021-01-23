# flax #

`flax` (FLAC-SQL) is a simple program that reads FLAC file metadata into a temporary SQLite database, tracks modifications of this database, then optionally writes the changes back to the FLAC files. It is not intended to replace other music tag editors, but (if you know SQL) it makes some large-scale batch changes much easier.

# Short Example #

    ~/Documents/Code/flax 17:07 % bundle exec ./flax /nas/media/TestMusic
    Loading files: 1515/1515 (100%)
    -- Loading resources from /home/fraser/.sqliterc
    SQLite version 3.34.0 2020-12-01 16:14:00
    Enter ".help" for usage hints.
    sqlite> .mode table
    sqlite> select artist, count(*) from music where artist like '%bach%' group by 1 order by 2 desc;
    +----------------------------+----------+
    |           ARTIST           | count(*) |
    +----------------------------+----------+
    | Bach, Johann Sebastian     | 346      |
    | Bach, JS                   | 48       |
    | Bach, Carl Philipp Emanuel | 33       |
    | J.S. Bach                  | 32       |
    | Erlebach, Philipp Heinrich | 9        |
    | Bach, Johann Christian     | 9        |
    | Johann Sebastian Bach      | 1        |
    +----------------------------+----------+
    sqlite> update music set artist='Bach, Johann Sebastian' where artist in ('Bach, JS','J.S. Bach','Johann Sebastian Bach');
    sqlite> ^D
    
    Deletions: 0; renames: 0; changes: 81; (s)save, (e)edit or (a)bandon changes? saving
    
# Installation #

Install Ruby, Ruby Bundler and SQLite3 using your distro utilities then, inside the project directory:

    $ bundle install --path=vendor/bundle

# Database Structure #
`flax` loads all data into a single table `music`. FLAC files use Vorbis comments, and `flax` uses the field names [proposed by Xiph](https://xiph.org/vorbis/doc/v-comment.html) as well as some common extras as column names. The full field list loaded is:

`title`, `version`, `album`, `tracknumber`, `artist`, `performer`, `copyright`, `license`, `organization`, `description`, `genre`, `date`, `location`, `contact`, `isrc`, `composer`, `tracktotal`, `discnumber`, `albumartist`

## Multi-value Fields ##

All Vorbis comment fields are multi-valued (that is, it is legal and common to have more than one value for each field name, for example, more than one artist). It would be possible to represent this properly in SQL using several tables with one-to-many or many-to-many relationships but the resulting structure would be extremely painful to work with. `flax` opts to load fields with multiple values into a single column with a magic separator character (by default `;`) so you must take care to avoid using this character as actual data.

# More Examples #

TBA
