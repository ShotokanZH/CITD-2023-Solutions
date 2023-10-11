# PAIN-REVERSE
The executable correctly stores the key in the "`body`" variable, so dumping the variable should be enough.

However, there are a couple big issues.
## The issues
- The key is generated from the binary content, so editing the binary means invalidating the key.
- While analyzing the binary with a debugger (gdb?) we can notice that the returned `body` variable is always different, what's happening?

## What's wrong with it
The function "`evolve_unicorn_dragon()` receives as parameter "`/proc/self/status`"
```c
[...]
int evolve_unicorn_dragon(const char *unicorn_file) {
    FILE *f = fopen(unicorn_file, "r");
    if (!f) {
        perror("Failed to open unicorn file");
        exit(1);
    }

    char blood[4096];
    fread(blood, sizeof(char), sizeof(blood) - 1, f);
    fclose(f);
    
    int rna[16] = {0};
    int count = 0;
    char *line = strtok(blood, "\n");
    while (line) {
        if (strlen(line) > 0) {
            char key[256];
            char value[256];
            sscanf(line, "%[^:]:%s", key, value);
            int v = (strspn(value, "0123456789") == strlen(value)) ? atoi(value) : -1;
            rna[count++] = v;
        }
        line = strtok(NULL, "\n");
    }

    int chimera = (rna[0] + 14 - rna[1] + rna[2] * rna[4] + rna[3] + rna[5] * rna[6]) * rna[7] * (rna[8] * rna[9] - rna[10] * rna[11] + rna[12] - rna[13] + rna[14] * rna[15]);
    int dragon = (chimera*4 + 1) * 11025;
    dragon = (chimera*7 + 1) * 20312 + dragon;
    return dragon;
}
[...]
```

The function takes a few numeric values from "`/proc/self/status`" and does a few mathematical operations with those values.

However, only one value is actually important in all of this, as it gets multiplied by all the other values:

```c
) * rna[7] * (
```

Specifically, the 8th row is: `TracerPid`, which by documentation is:
```
PID of process tracing this process (0 if not being traced).
```

So, by debugging the script we are actually setting `TracerPid` to != 0.

By setting `TracerPid` to 0 manually we can calculate a returning value of `31337`.

Knowing this we can solve it like so in gdb:

```
br get_magical_unicorn
br printf
r
c
c
select-frame 1
# optional, just prints stuff
info locals
# /optional
set dragon=31337
c
select-frame 1
info locals
# check the body variable
```

And then just run:
```shell
./pain-reverse --key {OBTAINEDKEY}
```
