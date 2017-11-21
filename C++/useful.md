## 解析命令行输入
```C++

void del_arg(int argc, char **argv, int index)
{
     //通过后项前移方式删除指定位置的字符串
    int i;
    for(i = index; i < argc-1; ++i) argv[i] = argv[i+1];
    argv[i] = 0; //补上空字符串
}

char *find_char_arg(int argc, char **argv, char *arg, char *def)
{
    //例如输入./darknet detector test --gpus 1,2,3
    //char *p = find_char_arg(args, argv, '--gpus', 0)
    int i;
    for(i = 0; i < argc-1; ++i){
        if(!argv[i]) continue;
        if(0==strcmp(argv[i], arg)){ //找到arg， 如'--gpus'
            def = argv[i+1]; //获取对应值, 如'1,2,3'
            del_arg(argc, argv, i);
            del_arg(argc, argv, i); //两次是因为'--gpus'和'1,2,3'都要删除
            break;
        }
    }
    return def;
}
```

## 解析配置文件
```c++

void strip(char *s)
{
    //删除字符串里的空白符
    size_t i;
    size_t len = strlen(s);
    size_t offset = 0;
    for(i = 0; i < len; ++i){
        char c = s[i];
        if(c==' '||c=='\t'||c=='\n') ++offset;
        else s[i-offset] = c;
    }
    s[len-offset] = '\0';
}

int read_option(char *s, list *options)
{
    
    size_t i;
    size_t len = strlen(s);
    char *val = 0;
    for(i = 0; i < len; ++i){
        if(s[i] == '='){
            s[i] = '\0'; //获取关键字
            val = s+i+1; //获取值
            break;
        }
    }
    if(i == len-1) return 0;
    char *key = s;
    option_insert(options, key, val);
    return 1;
}


list *read_data_cfg(char *filename)
{
    FILE *file = fopen(filename, "r");
    if(file == 0) file_error(filename);
    char *line;
    int nu = 0;
    list *options = make_list();
    while((line=fgetl(file)) != 0){
        ++ nu;
        strip(line);
        switch(line[0]){
            case '\0':
            case '#':
            case ';':
                free(line);
                break;
            default:
                if(!read_option(line, options)){
                    fprintf(stderr, "Config file error line %d, could parse: %s\n", nu, line);
                    free(line);
                }
                break;
        }
    }
    fclose(file);
    return options;
}
```
