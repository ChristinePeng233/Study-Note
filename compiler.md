```
E:	//转到E盘
cd E:\ComplierComplier\program  //change directory至目标路径
flex test.l		//此后会生成C文件lex.yy.c
gcc lex.yy.c	//使用gcc编译成可执行文件

```

```
// 这里写一些选项, 可以控制 Flex/Bison 的某些行为

%{

// 这里写一些全局的代码
// 因为最后要生成 C/C++ 文件, 实现主要逻辑的部分都是用 C/C++ 写的
// 难免会用到头文件, 所以通常头文件和一些全局声明/定义写在这里

%}

// 这里写一些 Flex/Bison 相关的定义
// 对于 Flex, 这里可以定义某个符号对应的正则表达式
// 对于 Bison, 这里可以定义终结符/非终结符的类型

%%

// 这里写 Flex/Bison 的规则描述
// 对于 Flex, 这里写的是 lexer 扫描到某个 token 后做的操作
// 对于 Bison, 这里写的是 parser 遇到某种语法规则后做的操作

%%

// 这里写一些用户自定义的代码
// 比如你希望在生成的 C/C++ 文件里定义一个函数, 做一些辅助工作
// 你同时希望在之前的规则描述里调用你定义的函数
// 那么, 你可以把 C/C++ 的函数定义写在这里, 声明写在文件开头

```

flex拥有一套简单的消除歧义的规则，使词法分析程序工作的两条规则是：1、flex模式只匹配输入字符或字符串一次；2、flex执行当前输入的最长可能匹配的动作。

flex的规则有着隐藏优先级，即匹配长度相同时，将最上面的规则作为匹配结果。

![1](E:\study information\myself\compiler\1.png)

**警告**：请不要直接用字符指针指向yytext，由于一些yytext内部实现的原因，这样可能会造成意想不到的错误。

```
%{

#include <stdio.h>
#include <stdlib.h>
#define LT					1
#define	LE					2
#define GT					3
#define	GE					4
#define	EQ					5
#define NE					6
#define LLK                 7
#define RLK                 8
#define LBK                 9
#define RBK                 10
#define EQU                 11
#define SEM                 12

#define BREAK 				15
#define CONST 				16
#define CONTINUE 			17
#define DO 					18
#define ELSE 				19
#define FOR 				20
#define IF 					21
#define INT 				22
#define MAIN 				23
#define RETURN 				24
#define VOID 				25
#define WHILE 				26

#define ID                  40
#define INTCONST            41
#define OPERATOR            42
#define BOUND            	43
#define ERRORCHAR           44

#define NEWLINE             50

#define ADD                 61
#define DEC                 62
#define MUL                	63
#define DIV                 64


%}

 
delim		[ \t\n]
ws			{delim}+
letter		[A-Za-z_]
digit		[0-9]
nonz_digit  [1-9]
o_digit     [0-7]
h_digit     [0-9a-fA-F]
id			{letter}({letter}|{digit})*
number		[0]|{nonz_digit}{digit}*
o_number    [0]{o_digit}+
h_number    [0][xX]{h_digit}+

/* 状态（条件）定义 */
%s COMMENT
%s COMMENT2
%%

<INITIAL>"//"			{BEGIN COMMENT;}
<COMMENT>\n				{BEGIN INITIAL;}
<COMMENT>.			 	{;}
<INITIAL>"/*"			{BEGIN COMMENT2;}
<COMMENT2>"*/"			{BEGIN INITIAL;}
<COMMENT2>.|\n			{;}


<INITIAL>{ws}	          {;}
<INITIAL>break			  {return (BREAK);}
<INITIAL>const			  {return (CONST);}
<INITIAL>continue		  {return (CONTINUE);}
<INITIAL>do		          {return (DO);}
<INITIAL>else			  {return (ELSE);}
<INITIAL>for			  {return (FOR);}
<INITIAL>if		          {return (IF);}
<INITIAL>int			  {return (INT);}
<INITIAL>main			  {return (MAIN);}
<INITIAL>return			  {return (RETURN);}
<INITIAL>void			  {return (VOID);}
<INITIAL>while			  {return (WHILE);}


<INITIAL>{id}	          {return (ID);}
<INITIAL>{number}	      {return (INTCONST);}
<INITIAL>{o_number}    	  {return (INTCONST);}
<INITIAL>{h_number}		  {return (INTCONST);}

<INITIAL>"+"	          {return (OPERATOR);}
<INITIAL>"-"	          {return (OPERATOR);}
<INITIAL>"*"	          {return (OPERATOR);}
<INITIAL>"/"	          {return (OPERATOR);}
<INITIAL>"!"	          {return (OPERATOR);}
<INITIAL>"%"	          {return (OPERATOR);}
<INITIAL>"&&"	          {return (OPERATOR);}
<INITIAL>"||"	          {return (OPERATOR);}
<INITIAL>"="	          {return (OPERATOR);}
<INITIAL>"=="	          {return (OPERATOR);}
<INITIAL>"!="	          {return (OPERATOR);}
<INITIAL>"<"	          {return (OPERATOR);}
<INITIAL>"<="	          {return (OPERATOR);}
<INITIAL>">"	          {return (OPERATOR);}
<INITIAL>">="	          {return (OPERATOR);}

<INITIAL>"("	          {return (BOUND);}
<INITIAL>")"	          {return (BOUND);}
<INITIAL>"{"	          {return (BOUND);}
<INITIAL>"}"	          {return (BOUND);}
<INITIAL>"["	          {return (BOUND);}
<INITIAL>"]"	          {return (BOUND);}
<INITIAL>";"	          {return (BOUND);}
<INITIAL>","	          {return (BOUND);}

<INITIAL>.				  {return ERRORCHAR;}

 
%%

int yywrap (){
  return 1;
}

void writeout(int c){
  switch(c){
  	case ERRORCHAR: fprintf(yyout, "ERRORCHAR %s", yytext);break;
  	case BOUND: fprintf(yyout, "BOUND %s", yytext);break;
  	case OPERATOR: fprintf(yyout, "OPERATOR %s", yytext);break;
    
	case BREAK: fprintf(yyout, "BREAK %s", yytext);break;
    case CONST: fprintf(yyout, "CONST %s", yytext);break;
    case CONTINUE: fprintf(yyout, "CONTINUE %s", yytext);break;
    case DO: fprintf(yyout, "DO %s", yytext);break;
    case ELSE: fprintf(yyout, "ELSE %s", yytext);break;
    case FOR: fprintf(yyout, "FOR %s", yytext);break;
    case IF: fprintf(yyout, "IF %s", yytext);break;
    case INT: fprintf(yyout, "INT %s", yytext);break;
    case MAIN: fprintf(yyout, "MAIN %s", yytext);break;
    case RETURN: fprintf(yyout, "RETURN %s", yytext);break;
    case VOID: fprintf(yyout, "VOID %s", yytext);break;
    case WHILE: fprintf(yyout, "WHILE %s", yytext);break;
    
	case INTCONST: fprintf(yyout, "IntConst %s", yytext);break;
    case ID: fprintf(yyout, "Ident %s", yytext);break;
    case NEWLINE: fprintf(yyout, "\n");break;
    default:break;
  }
  return;
}


int main (int argc, char ** argv){
	int c,j=0;
	if (argc>=2){
	  if ((yyin = fopen(argv[1], "r")) == NULL){
	    printf("Can't open file %s\n", argv[1]);
	    return 1;
	  }
	  if (argc>=3){
	    yyout=fopen(argv[2], "w");
	  }
	}

	while (c = yylex()){
		writeout(c);
		j++;
		writeout(NEWLINE);
	}

	if(argc>=2){
		fclose(yyin);
		if (argc>=3) 
			fclose(yyout);
	}
	return 0;
}
```

我自己写的：

```
%{
#include <stdio.h>
#include <stdlib.h>
	using namespace std;
	//定义(definations)
	//在这个部分可以使用C语言代码进行预处理，例如使用#include<stdio.h>，或是定义宏、常量等等。
#define IDENFR 1
#define INTCON 2
#define STRCON 3
#define MAINTK 4
#define	CONSTTK 5
#define INTTK 6
#define BREAKTK 7
#define IFTK 8
#define ELSETK 9
#define NOT 10
#define AND 11
#define OR 12
#define WHILETK 13
#define GETINTTK 14
#define PRINTFTK 15
#define RETURNTK 16
#define MINU 17
#define VOIDTK 18
#define MULT 19
#define DIV 20
#define MOD 21
#define LSS 22
#define LEQ 23
#define GRE 24
#define GEQ 25
#define EQL 26
#define NEQ 27
#define CONTINUETK 28
#define PLUS 29
#define ASSIGN 30
#define SEMICN 31
#define COMMA 32
#define LPARENT 33
#define RPARENT 34
#define LBRACK 35
#define RBRACK 36
#define LBRACE 37
#define RBRACE 38
#define ERRORCHAR  39
#define NEWLINE 40
%}

%%
//规则(rules)
/* 空白符和注释 */
WhiteSpace[\t\n\r] *
LineComment1   "//".*


/* 标识符 */
Identifier[a - zA - Z_][a - zA - Z0 - 9_] *
letter[A - Za - z_]
/* 整数字面量 */
Decimal[1 - 9][0 - 9] *
Octal         0[0 - 7] *
Hexadecimal   0[xX][0 - 9a - fA - F] +

/* 状态（条件）定义 */
% s COMMENT
% s COMMENT2


%%

//代码(user code)
<INITIAL>"//"			{BEGIN COMMENT; }
<COMMENT>\n{ BEGIN INITIAL; }
<COMMENT>.			 	{; }
< INITIAL>"/*"			{BEGIN COMMENT2; }
< COMMENT2>"*/"			{BEGIN INITIAL; }
<COMMENT2>. | \n{ ; }


<INITIAL>{Identifier} {; }
<INITIAL>break			  {return (BREAKTK); }
<INITIAL>const			  {return (CONSTTK); }
<INITIAL>continue		  {return (CONTINUETK); }
<INITIAL>else { return (ELSETK); }
<INITIAL>if		          {return (IFTK); }
<INITIAL>int{ return (INTTK); }
<INITIAL>main{ return (MAINTK); }
<INITIAL>return			  { return (RETURNTK); }
<INITIAL>void{ return (VOIDTK); }
<INITIAL>while			  {return (WHILETK); }


<INITIAL>{number} {return (INTCON); }
<INITIAL>{o_number} {return (INTCON); }
<INITIAL>{h_number} {return (INTCON); }

< INITIAL>"+"	          {return (PLUS); }
< INITIAL>"-"	          {return (MINU); }
< INITIAL>"*"	          {return (MULT; }
< INITIAL>"/"	          {return (DIV); }
< INITIAL>"!"	          {return (NOT); }
< INITIAL>"%"	          {return (MOD); }
< INITIAL>"&&"	          {return (AND); }
< INITIAL>"||"	          {return (OR); }
< INITIAL>"="	          {return (ASSIGN); }
< INITIAL>"=="	          {return (EQL; }
< INITIAL>"!="	          {return (NEQ); }
< INITIAL>"<"	          {return (LSS); }
< INITIAL>"<="	          {return (LEQ); }
< INITIAL>">"	          {return (GRE); }
< INITIAL>">="	          {return (GEQ); }

< INITIAL>"("	          {return (LPARENT); }
< INITIAL>")"	          {return (RPARENT); }
< INITIAL>"{"	          {return (LBRACE); }
< INITIAL>"}"	          {return (RBRACE); }
< INITIAL>"["	          {return (LBRACK); }
< INITIAL>"]"	          {return (RBRACK); }
< INITIAL>";"	          {return (SEMICN); }
< INITIAL>","	          {return (COMMA); }

<INITIAL>.				  {return ERRORCHAR; }


void writeout(int c) {
    switch (c) {
    case ERRORCHAR: fprintf(yyout, "ERRORCHAR %s", yytext); break;
    case LPARENT: fprintf(yyout, "LPARENT %s", yytext); break;
    case RPARENT: fprintf(yyout, "RPARENT %s", yytext); break;
    case LBRACK: fprintf(yyout, "LBRACK %s", yytext); break;
    case RBRACK: fprintf(yyout, "RBRACK %s", yytext); break;
    case LBRACE: fprintf(yyout, "LBRACE %s", yytext); break;
    case RBRACE: fprintf(yyout, "LBRACE %s", yytext); break;
    case PLUS: fprintf(yyout, "PLUS %s", yytext); break;
    case MINU: fprintf(yyout, "MINU %s", yytext); break;
    case MULT: fprintf(yyout, "MULT %s", yytext); break;
    case DIV: fprintf(yyout, "DIV %s", yytext); break;
    case MOD: fprintf(yyout, "MOD %s", yytext); break;
    case NOT: fprintf(yyout, "NOT %s", yytext); break;
    case AND: fprintf(yyout, "AND %s", yytext); break;
    case OR: fprintf(yyout, "OR %s", yytext); break;
    case EQL: fprintf(yyout, "EQL %s", yytext); break;
    case NEQ: fprintf(yyout, "NEQ %s", yytext); break;
    case ASSIGN: fprintf(yyout, "ASSIGN %s", yytext); break;
    case LEQ: fprintf(yyout, "LEQ %s", yytext); break;
    case LSS: fprintf(yyout, "LSS %s", yytext); break;
    case GRE: fprintf(yyout, "GRE %s", yytext); break;
    case GEQ: fprintf(yyout, "GEQ %s", yytext); break;



    case BREAKTK: fprintf(yyout, "BREAKTK %s", yytext); break;
    case INTCON: fprintf(yyout, "INTCON %s", yytext); break;
    case CONTINUETK: fprintf(yyout, "CONTINUETK %s", yytext); break;
    case ELSETK: fprintf(yyout, "ELSETK %s", yytext); break;
    case IFTK: fprintf(yyout, "IFTK %s", yytext); break;
    case INTTK: fprintf(yyout, "INTTK %s", yytext); break;
    case MAINTK: fprintf(yyout, "MAINTK %s", yytext); break;
    case RETURNTK: fprintf(yyout, "RETURNTK %s", yytext); break;
    case VOIDTK: fprintf(yyout, "VOIDTK %s", yytext); break;
    case WHILETK: fprintf(yyout, "WHILETK %s", yytext); break;
    case NEWLINE: fprintf(yyout, "\n"); break;
    default:break;
    }
    return;
}


//约束函数，当它返回1时，代表扫描结束，此时结束程序。这个函数在读取多个文件时很有用处
int yywrap()
{
	return 1;
}

int main(int argc, char** argv) {
    int c, j = 0;
    if (argc >= 2) {
        if ((yyin = fopen(argv[1], "r")) == NULL) {
            printf("Can't open file %s\n", argv[1]);
            return 1;
        }
        if (argc >= 3) {
            yyout = fopen(argv[2], "w");
        }
    }

    while (c = yylex()) {
        writeout(c);
        j++;
        writeout(NEWLINE);
    }

    if (argc >= 2) {
        fclose(yyin);
        if (argc >= 3)
            fclose(yyout);
    }
    return 0;
}

```

