# Docker-CLI-CSV-formatter
Format CSV files using Docker CLI command piping and save the result into a txt file.<br />
Two specified commands below format and save the result like:
```
one,two,three ->
1 One
2 Two
3 Three
```
These commands require you to have the 'ubuntu:latest' and 'busybox:latest' images already downloaded because I set the container's --pull policy to 'never',
or you can remove this flag if you want to. <br />

I'm using Windows, for Linux swap ` with \

The first command will format a specified .csv file and save (append) the result to a newly created or already existed named volume. <br />
```
cat <path-to-csv-file> `
  | docker container run -i --rm --pull never ubuntu tr ',' '\n' `
  | docker container run -i --rm --pull never ubuntu sed 's/./\u&/' `
  | docker container run -i --rm --pull never ubuntu nl -s ' ' -w 1 `
  | docker container run -i --rm --pull never --mount src=fixed-files,dst=/fixed-files `
  ubuntu sh -c "cat - >> /fixed-files/formatted-file.txt"
```

The second command will save the 'formatted-file.txt' from the 'fixed-files' volume to your machine. You need to have the '$(pwd)/processed-files' directory
already created before running this command, because '--mount' unlike '-v' doesn't create it for you.
```
docker container run --rm --pull never `
  --mount type=bind,src=$(pwd)/processed-files,dst=/bind-processed-files `
  --mount src=fixed-files,dst=/volume-processed-files `
  busybox cp /volume-processed-files/formatted-file.txt /bind-processed-files/formatted-file.txt
```
