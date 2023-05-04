Download Link: https://assignmentchef.com/product/solved-cse-340-project-4-passed-all-the-tests
<br>
<header role="banner">

 <hgroup></hgroup>

</header>

<footer role="contentinfo">




</footer>

5/5 - (2 votes)

<article role="article">

 Your goal is to finish an incomplete parser and write a type checker for a given language. The input to your project will be a program and the output will be either error messages if there is a type mismatch or a success message if there is no type mismatch. Your type checker will enforce semantic checks on the input program, and will be described in the following.




 <h2><a id="grammar-description"></a>Grammar Description</h2>

 <pre><code>program → decl bodydecl → type_decl_section var_decl_sectiontype_decl_section → TYPE type_decl_listtype_decl_section → εtype_decl_list → type_decl type_decl_listtype_decl_list → type_decltype_decl → id_list COLON type_name SEMICOLONtype_name → REALtype_name → INTtype_name → BOOLEANtype_name → STRINGtype_name → LONGtype_name → IDvar_decl_section → VAR var_decl_listvar_decl_section → εvar_decl_list → var_decl var_decl_listvar_decl_list → var_declvar_decl → id_list COLON type_name SEMICOLONid_list → ID COMMA id_listid_list → IDbody → LBRACE stmt_list RBRACEstmt_list → stmt stmt_liststmt_list → stmtstmt → while_stmtstmt → assign_stmtstmt → do_stmtstmt → switch_stmtwhile_stmt → WHILE condition bodyassign_stmt → ID EQUAL expr SEMICOLONdo_stmt → DO body WHILE condition SEMICOLONswitch_stmt → SWITCH ID LBRACE case_list RBRACEcase_list → case case_listcase_list → casecase → CASE NUM COLON bodyexpr → term PLUS exprexpr → term MINUS exprexpr → termterm → factor MULT termterm → factor DIV termterm → factorfactor → LPAREN expr RPARENfactor → NUMfactor → REALNUMfactor → IDcondition → IDcondition → primary relop primaryprimary → IDprimary → NUMprimary → REALNUMrelop → GREATERrelop → GTEQrelop → LESSrelop → NOTEQUALrelop → LTEQ</code></pre>

 The tokens used in the grammar description are:

 <pre><code>TYPE = TYPECOLON = :SEMICOLON = ;REAL = REALINT = INTBOOLEAN = BOOLEANSTRING = STRINGLONG = LONGVAR = VARCOMMA = ,LBRACE = {RBRACE = }WHILE = WHILEEQUAL = =DO = DOSWITCH = SWITCHCASE = CASEPLUS = +MINUS = -MULT = *DIV = /LPAREN = (RPAREN = )GREATER = &gt;GTEQ = &gt;=LESS = &lt;LTEQ = &lt;=NOTEQUAL = &lt;&gt;ID = letter(letter | digit)*NUM = 0 | (digit digit*)REALNUM = NUM . digit*</code></pre>

 <h2>Language Semantics</h2>

 As can be seen from the grammar, in this language types are first declared, then variables are declared, then the body of the program follows.

 <h3>Types</h3>

 The language has five built-in types: INT, REAL, BOOLEAN, STRING, and LONG.

 Programmers can declare types either explicitly or implicitly.

 <ul>

  <li>Explicit types are names that are not built-in types and that have their first appearance in the program as part of the <code>id_list</code> of a <code>type_decl</code>.</li>

  <li>Implicit types are not built-in types and not explicit user-declared types. Implicit types have their first appearance as a <code>type_name</code> in a <code>var_decl</code>.</li>

 </ul>

 <h4>Example</h4>

 Consider the following program written in our language:

 <pre><code>TYPE  a : INT;  b : a;VAR  x : b;  y : c;{  y = x;}</code></pre>

 There are three types declared by the programmer in this example, <code>a</code>, <code>b</code>, and <code>c</code>, where <code>a</code> and <code>b</code> are explicit types and <code>c</code> is an implicit type.

 <h3>Variables</h3>

 Programmers can declare variables either explicitly or implicitly.

 <ul>

  <li>Explicit variables are declared in an <code>id_list</code> of a <code>var_decl</code>.</li>

  <li>Implicit variables are declared if it is not declared explicitly but it appears in the program body.</li>

 </ul>

 <h4>Example</h4>

 Consider the following program written in our language:

 <pre><code>TYPE  a : INT;  b : a;VAR  x : b;  y : c;{  y = x;  z = 10;  w = z * 5;}</code></pre>

 This program has four variables declared: <code>x</code>, <code>y</code>, <code>z</code>, and <code>w</code>, with <code>x</code> and <code>y</code> explicitly declared and <code>z</code> and <code>w</code> implicitly declared. Note that the implicitly declared variables <code>z</code> and <code>w</code> also have an implicitly declared type.

 <h3>Type System</h3>

 Our language uses structural equivalence for checking type equivalence.

 Implicit types (in variable declarations or on implicitly declared variables) will be inferred from the usage (in a simplified form of Hindley-Milner type inference).

 Here are all the type rules/constraints that your type checker will enforce:

 <ul>

  <li>Assignment cannot be made between variables of different types</li>

  <li>Operations (<code>PLUS</code>, <code>MINUS</code>, <code>MULT</code>, and <code>DIV</code>) cannot be applied to operands of different types (but can be applied to operands of the same type, including <code>STRING</code> and <code>BOOLEAN</code>)</li>

  <li>Relational operators (<code>relop</code>) cannot be applied to operands of different types (but can be applied to operands of the same type, including <code>STRING</code> and <code>BOOLEAN</code>)</li>

  <li>The result of <code>p1</code> <code>relop</code> <code>p2</code> is of type <code>BOOLEAN</code> (assuming that both <code>p1</code> and <code>p2</code> are the same type)</li>

  <li><code>condition</code> should be of type <code>BOOLEAN</code></li>

  <li>The type of an <code>expr</code> is the same as the type of its operands</li>

  <li>The variable that follows the <code>SWITCH</code> keyword in <code>switch_stmt</code> should be of type <code>INT</code></li>

  <li><code>NUM</code> constants are of type <code>INT</code></li>

  <li><code>REALNUM</code> constants are of type <code>REAL</code></li>

 </ul>

 <h2>Incomplete Parser</h2>

 The <a href="https://cse340.fulton.asu.edu/cse340/">parser given on the submission site</a> is incomplete, as it is missing an implementation for <code>while_stmt</code>, <code>condition</code>, <code>do_stmt</code>, <code>switch_stmt</code>, <code>case_list</code>, and <code>case</code>.

 As described in the <a href="https://adamdoupe.com/teaching/classes/cse340-principles-of-programming-languages-f15/project-4.html#evaluation">evaluation section</a>, you must implement parsing for <code>while_stmt</code>, <code>condition</code>, and <code>do_stmt</code>, while <code>switch_stmt</code>, <code>case_list</code>, and <code>case</code> are extra credit (though note that to receive full extra credit, you must implement all of the type checks in addition to the parsing cases.

 <h2>Semantic Error Output</h2>

 Your program will check for the following semantic errors (with type checking being one of those semantic errors) and output the correct output when it encounters that error. Note that there will only be at most one error per test case.

 <h3>Type name declared more than once</h3>

 If a type name is declared more than once, either implicitly or explicitly, then the output of your program should be:

 <pre><code>ERROR CODE 0 &lt;type_name&gt;</code></pre>

 Where <code>&lt;type_name&gt;</code> is replaced with the name that is declared more than once.

 <h3>Type re-declared as variable</h3>

 If a type name is redeclared as a variable, either implicitly or explicitly, then the output of your program should be:

 <pre><code>ERROR CODE 1 &lt;type_name&gt;</code></pre>

 Where <code>&lt;type_name&gt;</code> is replaced with the name of the type that is re-declared as a variable.

 <h3>Variable declared more than once</h3>

 If a variable name is declared more than once, either implicitly or explicitly, then the output of your program should be:

 <pre><code>ERROR CODE 2 &lt;variable_name&gt;</code></pre>

 Where <code>&lt;variable_name&gt;</code> is replaced with the name of the variable that is declared more than once.

 <h3>Type Mismatch</h3>

 If there is a type mismatch in the program, then the output of your program should be:

 <pre><code>ERROR CODE 3 &lt;line_number&gt;</code></pre>

 Where <code>&lt;line_number&gt;</code> is replaced with the line number that the type mismatch occurs on. Note that you can assume that anywhere a type error can occur will be on a single line.

 <h3>Variable re-declared as type name</h3>

 If a variable is re-declared as a type name, either implicitly or explicitly, then the output of your program should be:

 <pre><code>ERROR CODE 4 &lt;variable_name&gt;</code></pre>

 Where <code>&lt;variable_name&gt;</code> is replaced with the name of the variable that is re-declared as a type.

 <h3>No semantic errors</h3>

 If there are no semantic errors in the program, then your output should be the following:

 <pre><code>All systems go!</code></pre>

 <h2>Examples</h2>

 Given the following:

 <pre><code>TYPE  a,b,c,b : INT;VAR  x : a;{  x = 10;}</code></pre>

 The output will be the following:

 <pre><code>ERROR CODE 0 b</code></pre>

 Given the following:

 <pre><code>TYPE  a : INT;VAR  x : INT;  b, a : STRING;{  x = 10;}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 1 a</code></pre>

 Given the following:

 <pre><code>VAR  x1 : INT;  x2, x3, x1 : a;{  x1 = 0;}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 2 x1</code></pre>

 Given the following:

 <pre><code>VAR  x100 : INT;  y : STRING;{  x100 = y;}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 3 5</code></pre>

 Given the following:

 <pre><code>VAR  x : INT;{  x = 100;  y = 20.10;  y = x;}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 3 6</code></pre>

 Given the following:

 <pre><code>VAR  x, y : a1;{  WHILE x &lt;&gt; 10  {    x = x + y;    y = y * 1.0;  }}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 3 7</code></pre>

 Given the following:

 <pre><code>VAR  x, y : STRING;  z : x;{  y = x;}</code></pre>

 The output should be the following:

 <pre><code>ERROR CODE 4 x</code></pre>

 Given the following:

 <pre><code>TYPE  a, b : INT;  c : a;  d : STRING;VAR  x : e;  y : c;  test : d;{  a1 = 100;  b1 = a1 + (10 - 50);  foo = b1 / 50;  SWITCH foo  {    CASE 1:    {      foo = 0;    }    CASE 2:    {      test = test * test;    }  }}</code></pre>

 The output should be the following:

 <pre><code>All systems go! </code></pre>

 <h3><a id="evaluation"></a>Evaluation</h3>

 Your submission will be graded on passing the automated test cases.

 The test cases (there will be multiple test cases in each category, each with equal weight) will be broken down in the following way (out of 100 points):

 <ul>

  <li>Type error 0 (type name declared more than once) : 10 points</li>

  <li>Type error 1 (type re-declared as variable) : 10 points</li>

  <li>Type error 2 (variable declared more than once) : 10 points</li>

  <li>Type error 3 (type mismatch) : 60 points</li>

  <li>Type error 4 (variable re-declared as a type name) : 10 points</li>

  <li>Implementing parsing for <code>case</code>, <code>case_list</code>, and <code>switch_stmt</code>: 5 points EC</li>

 </ul>

 Also, there will be a 50% penalty for Improperly Implemented Parser, so make sure that you implement the parser correctly!

 Note that test cases must match the output <strong>exactly</strong>, and no partial credit will be given.