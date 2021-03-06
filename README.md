# JointyC Library
	                                
The scope of this README is to give an overview of the library.
For more details see the following:

* Wiki: https://github.com/sv-giampa/JointyC-Library/wiki

* JavaDoc: https://sv-giampa.github.io/JointyC-Library/

* Tutorials Wiki: https://github.com/sv-giampa/JointyC-Tutorials/wiki

* Tutorials and template project: https://github.com/sv-giampa/JointyC-Tutorials

## Introduction

JointyC is a Java library to write compilers. Its main target is to move
the attention of the developer from parsing to the semantic analysis and,
at the same time, to simplify this part of work.
The library is divided in two main modules:

1. The analysis framework

2. The JointyC Definition Language Compiler (JDLC)

The first module exposes a framework to face up to the analysis work,
composed by lexical, syntactic and semantic analysis.
The second module is based on the first one and provides a compiler for
the JointyC Definition Language (JDL), used to define recursive-descent
parsers for context-free languages, that can be adorned with contextual
information during semantic analysis.

Follows a fast presentation of the library and more detailed introduction.

# A very fast presentation of the idea
After this very short description, really few things will be clear, but probably the power of JointyC will be one of these.

Execute three main steps to write a compiler with JointyC:

1) write your language specifics in the JointyC Definition Language:
```
//Language.jdl file
language: yourLanguage;

lexicon:{
	myToken = /token1/$, "description of token";
	mySecondToken = /token2/$, "description of the second token";
}

grammar:{
	axiom = nonTerminal $myToken;
	nonTerminal = $mySecondToken;
}
```

2) write the interpreter of the language:
```
//MyInterpreter.java file
class MyInterpreter implements Interpreter{

	@TerminalToken(type="yourLanguage.myToken") 		//bind the "firstToken" method to the "myToken" token
	private String firstToken(){
		return "myToken";
	}
	
	@TerminalToken(type="yourLanguage.mySecondToken") 	//bind the "secondToken" method to the "mySecondToken" token
	private String secondToken(SyntaxTree tree){
		return tree.token();
	}
	
	@NonTerminalToken(ruleHead="yourLanguage.axiom", ruleProduction = {"yourLanguage.nonTerminal", "$yourLanguage.myToken"}) //bind the "computeAxiom" method to the non-temrinal "axiom"
	private String computeAxiom(String firstToken, String secondToken){
		return firstToken + " - " + secondToken;
	}
}
```

3) instantiate the compiler:
```
//some main() method or façade
JdlCompiler jdlc = new JdlCompiler();
StandardCompiler compiler = jdlc.compileResource("Language.jdl", new MyInterpreter());

//...then use the compiler...
String compilationResult = compiler.compile("  token1   token2 ");
System.out.println(compilationResult); //prints "token1 - token2"
```

And now, please continue reading.

# Goals of the library
This is a more detailed presentation of the library.

## Software Engineering in the scope

As anticipated, the most important goal of JointyC is to simplify the
work of the developers, not only during construction, but also during 
maintenance. In fact, writing a recursive descent parser may result in
a difficult maintenance task. On the other hand, using a parser generator
tool, may be very invasive. It was thought the best way is to have a
simple to use library without any other dependencies or tools and
automatically re-compile the parser, without affecting heavily the
semantic analysis, when a change to the grammar is necessary. By using
the JointyC Definition Language, the parser of the designed language will
be re-compiled at every construction. The creation of the parser is moved
at runtime and totally automated. The parser is compiled only at
construction time, so the parsing proceed easily after that operation.
Moreover a compiler can be serialized by using the standard serialization
process of Java. This process is useful if it is needed more preformance 
during the compiler loading, but it is required to build the compiler
before the deployment phase of the project (it will not be built at
runtime, but only loaded).
The target, from the point of view of Software Engineering, is to write
a parser according to its language specifics, and to modify it when the
language specifics are changed or extended.
More details about this process and other functionalities are reported
in the documentation and in the wiki.


## The analysis framework

The analysis framework is divided in three sub-modules: lexer, parser and
semantic analyzer. The framework provides all the interfaces that define
the functionalities for lexers, parsers and interpreters.
Lexer and parser modules define proper final implementations, named
with the "Standard" prefix: the StandardLexer and the StandardParser.
The semantic module provides a standard syntax tree exploring machinery,
defined by the SemanticAnalyzer class. This class implements an iterative
algorithm which explores a syntax tree obtained by a parser and calls
the associated interpreter to do semantic actions. The three standard
structures are assembled in the StandardCompiler class, to expose a
functional compiler object basing on a parser and an interpreter passed
at construction time. So it is simple to obtain a lexer, a parser and
then a compiler for a language, starting from production rules, without
necessarily use the JDL Compiler. Moreover, it is recommended to use the
JDL Compiler, to obtain a higher maintenance and readability performance.


## The JointyC Definition Language Compiler (JDL Compiler)

By using the JDL Compiler it is possible to define and compile a parser
and the associated lexer at run-time. The objective is to have a double
phase process: the design of the language's specifics, first, and the
definition of the semantic operations, later. By using, the JDL it is
possible to divide the languages in modules, and at the same time,
it is possible to design one interpreter for each language module. Then
different modules and their interpreters may be composed in a single
work. A JDL file is divided in two parts: lexicon and grammar. To define
a lexicon for the language, regular expressions are used. The grammar is
expressed in BNF (Backus-Naur Form), because, in many cases, the BNF is
simpler to manage during semantic analysis than the EBNF (Extended BNF).
A grammar rule cannot produce directly a ground terminal, but each
terminal symbol must be assigned to a term of the lexicon. This choice
allows to keep the grammar and the access to the syntax tree as generic
as possible.


## Conclusions and recommendations

For the explained reasons, the JointyC library could be one of the best
choices for designing a language for some project. It is emphasized
that this library is under Apache License 2.0, and then it could be
integrated also in a proprietary software, or used as a starting point
to obtain an enhanced version of it. To familiarize with JointyC and its
programming logics, it is highly recommended to start exploring the
provided tutorials and documentation.
		
		
		
		
		
		
		

