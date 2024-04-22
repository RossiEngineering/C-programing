# student
#include<stdio.h>

int main(){
int a=33, b=22;
FILE* fp = fopen("dat.txt","w");
fprintf(fp, "%d\n", a, b);
fclose(fp);
}
