MyScheme
============

`Github 链接 <https://github.com/MiliLong/MyScheme>`_
*********************************************************

源代码
********

    .. code-block:: python

        """
        Define some classes to assist.
        """
        from enum import Enum

        class TokenType(Enum):
            """
            Define the enumeration class.
            """
            NAME = 1
            NUMBER = 2
            LPAREN = 3
            RPAREN = 4
            OPERATOR = 5

        class Token:
            """
            Define the token class.
            """    
            def __init__(self, type, value):
                self.type = type
                self.value = value

        class Node:
            """
            Define the node class.
            """ 
            def __init__(self, body):
                self.body = body
            def __getitem__(self, key):
                return self.body[key]
            def __len__(self):
                return len(self.body)
            
        class Function(Node):
            """
            Define the function class.
            """  
            def __init__(self, args, body):
                super().__init__(body)
                self.args = args
    .. code-block:: python

        """
        A scheme-like dialect, with only floating point data types.
        Only basic arithmetic operations are supported.
        Basic functions such as conditional judgment and functions are available.
        Can be used for entertainment and learning purposes.
        """
        from object import TokenType as TType
        from object import Token
        from object import Node
        from object import Function
        import argparse
        import re

        """
        Add parameters and get parameters.
        """
        parser = argparse.ArgumentParser(description="")
        parser.add_argument("--file", type=str, required=True, help="")
        args = parser.parse_args()

        """
        Reading code text.
        """
        try:
            with open(args.file, mode="r",encoding="UTF-8") as f:
                code_text = f.read()
        except FileNotFoundError:
            print(f"Error : '{args.file}' not found.")
        except IOError:
            print(f"Error : Error reading {args.file}.")

        """
        Tokenize.
        """
        strings = code_text.replace("(", " ( ").replace(")", " ) ").replace("\n", " ").split(" ")
        strings =[string for string in strings if len(string)]
        tokens = []
        for string in strings:
            if string == "(":
                tokens.append(Token(TType.LPAREN, string))
            elif string == ")":
                tokens.append(Token(TType.RPAREN, string))
            elif bool(re.match(r'^[a-zA-Z_][a-zA-Z0-9_]*$', string)):
                tokens.append(Token(TType.NAME, string))
            elif bool(re.match(r'^[-+]?[0-9]*\.?[0-9]+([eE][-+]?[0-9]+)?$', string)):
                tokens.append(Token(TType.NUMBER, float(string)))
            else:
                tokens.append(Token(TType.OPERATOR, string))

        """
        Constructing AST.
        """
        AST, node = [], []
        for token in tokens:
            if token.type != TType.RPAREN:
                AST.append(token)
            else:
                while isinstance(AST[-1], Node) or AST[-1].type != TType.LPAREN:
                    node.append(AST[-1])
                    AST.pop()
                AST.pop()
                AST.append(Node(node[::-1]))
                node = []

        AST = Node(AST)

        # def easyprint(node):
        #     if isinstance(node, Token):
        #         print(f"[{node.type}, {node.value}]", end="")
        #     else:
        #         print(end="[")
        #         for item in node:
        #             easyprint(item)
        #         print(end="]")
        # easyprint(AST)

        """
        Executing code.
        """
        symbol_table = {}
        def execute(node, table):
            if isinstance(node, Token):
                if node.type == TType.NUMBER:
                    return node.value
                elif node.type == TType.NAME:
                    if node.value in table:
                        return table[node.value]
                    elif node.value in symbol_table:
                        return symbol_table[node.value]
                    else:
                        print(f"Error: '{node.value}' is not defined.")
                        exit(1)
                else:
                    print(f"Error: '{node.value}' is not a valid token.")
                    exit(1)
            elif isinstance(node[0], Token):
                if node[0].type == TType.NAME:
                        if node[0].value == "exit": 
                            exit(int(execute(node[1], table)))   
                        elif node[0].value == "begin":
                            for item in node[1:-1]:
                                execute(item, table)
                            return execute(node[-1], table)
                        elif node[0].value == "input":
                            return float(input())
                        elif node[0].value == "output":
                            result = execute(node[1], table)
                            print(result)
                            return result
                        elif node[0].value == "define":
                            result = execute(node[2], table)
                            symbol_table[node[1].value] = result
                            return result
                        elif node[0].value == "if":
                            if execute(node[1], table):
                                return execute(node[2], table)
                            else:
                                return execute(node[3], table)
                        elif node[0].value == "lambda":
                            return Function(node[1][::], node[2][::])
                        else:
                            fuc = None
                            if node[0].value in table:
                                fuc = table[node[0].value]
                            elif node[0].value in symbol_table:
                                fuc = symbol_table[node[0].value]
                            if fuc:
                                l = len(fuc.args)
                                _table = table.copy()
                                for i in range(min(l, len(node[1]))):
                                    _table[fuc.args[i].value] = execute(node[1][i], table)
                                return execute(Node(fuc.body), _table)
                            else:
                                print(f"Error: '{node[0].value}' is not valid.")
                                exit(1)
                elif node[0].type == TType.OPERATOR:
                    if node[0].value == "+":
                        return execute(node[1], table) + execute(node[2], table)
                    elif node[0].value == "-":
                        return execute(node[1], table) - execute(node[2], table)
                    elif node[0].value == "*":
                        return execute(node[1], table) * execute(node[2], table)
                    elif node[0].value == "/":
                        return execute(node[1], table) / execute(node[2], table)
                    else:
                        print(f"Error: '{node[0].value}' is not a valid operator.")
                        exit(1)
                else:
                    print(f"Error: '{node}' is not a valid node.")
                    exit(1)
            else:
                for item in node:
                    execute(item, table)
                return 0

        exit(execute(AST, {}))

    ::

        (define fib
            (lambda (n)
                (if n 
                    (if (- n 1)
                        (+ (fib ((- n 1))) (fib ((- n 2))))
                        1
                    )     
                    0
                )
            )
        )

        (define for
            (lambda (n fuc)
                (if n
                    (begin
                        (output (fuc (n)))
                        (for ((- n 1)))
                    )
                    0
                )
            )
        )

        (for ((input) fib))

        (exit 0)

评价
*****

    蛮有意思的哈哈。

    有一说一其实也蛮遗憾的，有很多想要的功能都没有实现。
    这种语言潜力其实很大的，但是鄙人能力时间有限，就留坑后补吧。

    展望一下，之后完全可以在支持大部分 `Scheme` 语法的情况下，当然不排除自己设计哦，用 `C/CPP` 实现解释器。

    `Python` 还是太过取巧了，而且完全可以把源码转成 `bytecode` 运行。
