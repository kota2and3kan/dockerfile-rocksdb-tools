# dockerfile-rocksdb-tools
![Build RocksDB tools (ldb and sst_dump)](https://github.com/kota2and3kan/dockerfile-rocksdb-tools/workflows/Build%20RocksDB%20tools%20(ldb%20and%20sst_dump)/badge.svg)  
Dockerfile for build RocksDB tools (ldb and sst_dump).


## Note
For test local or development environment.  
Not for production.


## Build
1.
Clone this repository.
```
$ git clone https://github.com/kota2and3kan/dockerfile-rocksdb-tools.git
$ cd dockerfile-rocksdb-tools
```

2.
Run `docker build`.
```
$ sudo docker build -t <image-name>:<tag-name> ./rocksdb-tools
```


## Usage

After build image, you can run container and use ldb and sst_dump command.

1.
Run and Attach Container.
```
$ sudo docker run -it --rm rocksdb-tools:latest
root@5a682e3b99c9:/#
```

2.
Run the ldb and sst_dump. (Example)
```
root@5a682e3b99c9:/# ldb --db=/db --create_if_missing put a1 b1
OK

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db get a1
b1

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db scan
a1 : b1

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db scan --hex
0x6131 : 0x6231

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db put --key_hex 0x6132 b2
OK

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db scan
a1 : b1
a2 : b2

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db get --value_hex a2
0x6232

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db get --hex 0x6131
0x6231

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db batchput a3 b3 a4 b4
OK

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db scan
a1 : b1
a2 : b2
a3 : b3
a4 : b4

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db batchput "multiple words key" "multiple words value"
OK

root@5a682e3b99c9:/#  
root@5a682e3b99c9:/# ldb --db=/db scan
a1 : b1
a2 : b2
a3 : b3
a4 : b4
multiple words key : multiple words value

root@5a682e3b99c9:/#
root@5a682e3b99c9:/# sst_dump --file=/db/ --command=scan
from [] to []
Process /db//000004.sst
Sst file format: block-based
'a1' seq:1, type:1 => b1
Process /db//000007.sst
Sst file format: block-based
'a2' seq:2, type:1 => b2
Process /db//000010.sst
Sst file format: block-based
'a3' seq:3, type:1 => b3
'a4' seq:4, type:1 => b4
```


## Help

You can see the help of each command by running it without argument.

### ldb
```
root@5a682e3b99c9:/# ldb
ldb - RocksDB Tool

commands MUST specify --db=<full_path_to_db_directory> when necessary

The following optional parameters control if keys/values are input/output as hex or as plain strings:
  --key_hex : Keys are input/output as hex
  --value_hex : Values are input/output as hex
  --hex : Both keys and values are input/output as hex

The following optional parameters control the database internals:
  --column_family=<string> : name of the column family to operate on. default: default column family
  --ttl with 'put','get','scan','dump','query','batchput' : DB supports ttl and value is internally timestamp-suffixed
  --try_load_options : Try to load option file from DB.
  --ignore_unknown_options : Ignore unknown options when loading option file.
  --bloom_bits=<int,e.g.:14>
  --fix_prefix_len=<int,e.g.:14>
  --compression_type=<no|snappy|zlib|bzip2|lz4|lz4hc|xpress|zstd>
  --compression_max_dict_bytes=<int,e.g.:16384>
  --block_size=<block_size_in_bytes>
  --auto_compaction=<true|false>
  --db_write_buffer_size=<int,e.g.:16777216>
  --write_buffer_size=<int,e.g.:4194304>
  --file_size=<int,e.g.:2097152>


Data Access Commands:
  put <key> <value>  [--ttl]
  get <key> [--ttl]
  batchput <key> <value> [<key> <value>] [..] [--ttl]
  scan [--from] [--to]  [--ttl] [--timestamp] [--max_keys=<N>q]  [--start_time=<N>:- is inclusive] [--end_time=<N>:- is exclusive] [--no_value]
  delete <key>
  deleterange <begin key> <end key>
  query [--ttl]
    Starts a REPL shell.  Type help for list of available commands.
  approxsize [--from] [--to]
  checkconsistency


Admin Commands:
  dump_wal --walfile=<write_ahead_log_file_path> [--header]  [--print_value]  [--write_committed=true|false]
  compact [--from] [--to]
  reduce_levels --new_levels=<New number of levels> [--print_old_levels]
  change_compaction_style --old_compaction_style=<Old compaction style: 0 for level compaction, 1 for universal compaction> --new_compaction_style=<New compaction style: 0 for level compaction, 1 for universal compaction>
  dump [--from] [--to]  [--ttl] [--max_keys=<N>] [--timestamp] [--count_only] [--count_delim=<char>] [--stats] [--bucket=<N>] [--start_time=<N>:- is inclusive] [--end_time=<N>:- is exclusive] [--path=<path_to_a_file>]
  load [--create_if_missing] [--disable_wal] [--bulk_load] [--compact]
  manifest_dump [--verbose] [--json] [--path=<path_to_manifest_file>]
  list_column_families full_path_to_db_directory
  create_column_family --db=<db_path> <new_column_family_name>
  drop_column_family --db=<db_path> <column_family_name_to_drop>
  dump_live_files
  idump [--from] [--to]  [--input_key_hex] [--max_keys=<N>] [--count_only] [--count_delim=<char>] [--stats]
  repair
  backup [--backup_env_uri]  [--backup_dir]  [--num_threads]  [--stderr_log_level=<int (InfoLogLevel)>]
  restore [--backup_env_uri]  [--backup_dir]  [--num_threads]  [--stderr_log_level=<int (InfoLogLevel)>]
  checkpoint [--checkpoint_dir]
  write_extern_sst <output_sst_path>
  ingest_extern_sst <input_sst_path> [--move_files]  [--snapshot_consistency]  [--allow_global_seqno]  [--allow_blocking_flush]  [--ingest_behind]  [--write_global_seqno]
```


### sst_dump
```
root@5a682e3b99c9:/# sst_dump
file or directory must be specified.

sst_dump --file=<data_dir_OR_sst_file> [--command=check|scan|raw]
    --file=<data_dir_OR_sst_file>
      Path to SST file or directory containing SST files

    --command=check|scan|raw|verify
        check: Iterate over entries in files but dont print anything except if an error is encounterd (default command)
        scan: Iterate over entries in files and print them to screen
        raw: Dump all the table contents to <file_name>_dump.txt
        verify: Iterate all the blocks in files verifying checksum to detect possible coruption but dont print anything except if a corruption is encountered
        recompress: reports the SST file size if recompressed with different
                    compression types

    --output_hex
      Can be combined with scan command to print the keys and values in Hex

    --from=<user_key>
      Key to start reading from when executing check|scan

    --to=<user_key>
      Key to stop reading at when executing check|scan

    --prefix=<user_key>
      Returns all keys with this prefix when executing check|scan
      Cannot be used in conjunction with --from

    --read_num=<num>
      Maximum number of entries to read when executing check|scan

    --verify_checksum
      Verify file checksum when executing check|scan

    --input_key_hex
      Can be combined with --from and --to to indicate that these values are encoded in Hex

    --show_properties
      Print table properties after iterating over the file when executing
      check|scan|raw

    --set_block_size=<block_size>
      Can be combined with --command=recompress to set the block size that will
      be used when trying different compression algorithms

    --compression_types=<comma-separated list of CompressionType members, e.g.,
      kSnappyCompression>
      Can be combined with --command=recompress to run recompression for this
      list of compression types

    --parse_internal_key=<0xKEY>
      Convenience option to parse an internal key on the command line. Dumps the
      internal key in hex format {'key' @ SN: type}
```


## License
Please refer the [LICENSE](https://github.com/kota2and3kan/dockerfile-rocksdb-tools/blob/master/LICENSE) for the license of the files in this repository.
