---
tags:
  - Systems_Programming
---
### `str.c`

```C
/* str.c: string library */

#include "str.h"

#include <ctype.h>
#include <stdbool.h>
#include <stdlib.h>

/* Functions */

/**
 * Convert string to lowercase.
 * @param   s	    String to convert
 * @param   w	    Pointer to buffer that holds result of conversion
 **/
void	str_lower(const char *s, char *w) {
    //TODO
    while (*s != '\0'){
        *w = tolower(*s);
        w++;
        s++;
    }
    *w = '\0';
}

/**
 * Convert string to uppercase.
 * @param   s	    String to convert
 * @param   w	    Pointer to buffer that holds result of conversion
 **/
void	str_upper(const char *s, char *w) {
    //TODO
    while (*s != '\0'){
        *w = toupper(*s);
        w++;
        s++;
    }
    *w = '\0';
}

/**
 * Convert string to titlecase.
 * @param   s	    String to convert
 * @param   w	    Pointer to buffer that holds result of conversion
 **/
void	str_title(const char *s, char *w) {
    // TODO
    
    while (*s != '\0'){
        while(isalpha(*s) == 0){
            *w = *s;
            w++;
            s++;
        }

        *w = toupper(*s);
        w++;
        s++;

        while(isalpha(*s) != 0){
            *w = tolower(*s);
            w++;
            s++;
        }
    }
    *w = '\0';
}

/**
 * Strip characters from back of string (if present).
 * @param   s	    String to strip
 * @param   chars   Characters to strip (if NULL, then all whitespace)
 * @param   w	    Pointer to buffer that holds result of strip
 **/
void	str_rstrip(const char *s, const char *chars, char *w) {
    // TODO
    
    const char *s_start = s;
    const char *chars_start = chars;

    if (*s == '\0'){
        *w = '\0';
        return;
    }

    if (chars == NULL){
        while (*s != '\0'){
            s++;
        }
        s--;
        while (isspace(*s)){
            s--;
        }
        s++;
        while (s_start != s){
            *w = *s_start;
            w++;
            s_start++;
        }
        *w = '\0';
        return;
    }

    int len = 0;
    while (*chars != '\0'){
        len+=1;
        chars++;
    }

    while (*s != '\0'){
        s++;
    }
    s--;

    int flag = 1;
    while (flag){
        flag = 0;
        const char *c = chars_start;
        while (*c != '\0'){
            if (*s == *c){
                flag =  1;
                break;
            }
            c++;
        }
        if (flag){
            s--;
        }
        chars = chars_start;
        if (s == s_start){
            break;
        }
    }
    s++;

    while (s_start != s){
        *w = *s_start;
        w++;
        s_start++;
    }
    *w = '\0';
}

/**
 * Delete characters from string.
 * @param   s	    String to delete from
 * @param   chars   Characters to delete
 * @param   w	    Pointer to buffer that holds result of deletion
 **/
void	str_delete(const char *s, const char *chars, char *w) {
    //TODO
    
    const char *chars_start = chars;
    while (*s != '\0'){
        int copy = 1;
        while (*chars != '\0'){
            if (*s == *chars){
                copy = 0;
                break;
            }
            chars++;
        }
        chars = chars_start;
        if (copy){
            *w = *s;
            w++;
        }
        s++;
    }
    *w = '\0';
}

/**
 * Translate characters in 'from' with corresponding characters in 'to'.
 * @param   s       String to translate
 * @param   from    String with characters to translate
 * @param   to      String with corresponding translation characters
 * @param   w	    Pointer to buffer that holds result of translation
 **/
void	str_translate(const char *s, const char *from, const char *to, char *w) {
    // TODO
    const char *from_start = from;
    const char *to_start = to;

    while(*s != '\0'){
        int tr=0;
        int index =0;

        while (*from != '\0'){
            if (*from == *s){
                tr =1;
                break;
            }
            index++;
            from++;
        }
        from = from_start;
        if (tr){
            const char *p = to_start;
            int i = 0;

            while(i<index && *p != '\0'){
                p++;
                i++;
            }

            if (*p != '\0'){
                *w = *p;
            } else {
                *w = *s;
            }
        
        } else {
            *w = *s;
        }
        
        w++;
        s++;
    }
    *w = '\0';
}

/* vim: set sts=4 sw=4 ts=8 expandtab ft=c: */
```