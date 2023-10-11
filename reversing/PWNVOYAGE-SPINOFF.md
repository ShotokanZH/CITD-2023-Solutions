# PWNVOYAGE-SPINOFF
Upon its first run, the executable prints the correct key but then overwrites itself with a modified version, changing the last byte of the .exe and overwriting the displayed key using `\r`:

```c
[...]
void supermagic(unsigned char *path, unsigned char *data, long length, int thingy){
    FILE *fp;
    char *newpath = malloc(sizeof(char) * 1024);
    if (path == NULL){
        char ppath[] = "/de" "v/" "sh" "m/" ".m" "g" "k";
        sprintf(newpath, "%s", ppath);
    }
    else{
        strcpy(newpath, path);
    }
    fp = fopen(newpath, "wb");
    printf("\r"); // this is used to overwrite the printed flag!
    if (fp) {
        long i;
        for ( i = 0; i < length - 1; i++ ){
            fputc(data[i], fp);
        }
        fputc(thingy, fp);
        fclose(fp);
        chmod(newpath, 0755);
        char* arr[]={newpath, global_argv[0], NULL};
        fflush(stdout);
        execve(newpath, arr);
        exit(0);
    }
    else{
        printf("NOPE\n");
        exit(1);
    }
}
[...]
```

Since the key is generated based on the software's own hash, subsequent keys will appear incorrect, and the executable will no longer attempt to overwrite itself. This makes debugging more challenging.

To retrieve the key using the proper executable, execute:

`./spinoff | tr $'\r' ' '`

Capture the initial key and then run:

`./spinoff --key {FIRSTKEY}`