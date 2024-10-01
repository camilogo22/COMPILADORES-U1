import re

class Token:
    def __init__(self, token_type, value, line):
        self.token_type = token_type
        self.value = value
        self.line = line

    def __repr__(self):
        return f"{self.token_type}: {self.value} (Línea {self.line})"

class Lexer:
    token_specification = [
        ('NUMBER',    r'\d+(\.\d*)?'),   # Números enteros o flotantes
        ('ASSIGN',    r'='),             # Operador de asignación
        ('END',       r';'),             # Fin de instrucción
        ('ID',        r'[A-Za-z_]\w*'),  # Identificadores
        ('OP',        r'[+\-*/]'),       # Operadores aritméticos
        ('NEWLINE',   r'\n'),            # Nuevas líneas
        ('SKIP',      r'[ \t]+'),        # Espacios y tabulaciones
        ('COMMENT',   r'#.*'),           # Comentarios
        ('MISMATCH',  r'.'),             # Cualquier otro carácter no esperado
    ]

    def __init__(self, source_code):
        self.token_regex = '|'.join(f'(?P<{pair[0]}>{pair[1]})' for pair in self.token_specification)
        self.source_code = source_code

    def tokenize(self):
        tokens = []
        line_num = 1
        for mo in re.finditer(self.token_regex, self.source_code):
            token_type = mo.lastgroup
            value = mo.group(token_type)
            if token_type == 'NEWLINE':
                line_num += 1
                continue
            elif token_type == 'SKIP':
                continue
            elif token_type == 'MISMATCH':
                raise SyntaxError(f'Unexpected character "{value}" at line {line_num}')
            else:
                tokens.append(Token(token_type, value, line_num))
        return tokens

class Node:
    def __init__(self, value):
        self.value = value
        self.children = []

    def add_child(self, child):
        self.children.append(child)

    def __repr__(self, level=0):
        ret = "\t" * level + f"{self.value}\n"
        for child in self.children:
            ret += child.__repr__(level + 1)
        return ret

class Parser:
    def __init__(self, tokens):
        self.tokens = tokens
        self.pos = 0

    def parse(self):
        statements = []
        while self.pos < len(self.tokens):
            stmt = self.statement()
            if stmt:
                statements.append(stmt)
        return statements

    def statement(self):
        if self.match('ID'):
            var_node = Node(self.previous().value)
            self.consume('ASSIGN')
            expr_node = self.expression()
            self.consume('END')
            assign_node = Node('=')
            assign_node.add_child(var_node)
            assign_node.add_child(expr_node)
            return assign_node
        return None

    def expression(self):
        left = self.term()
        while self.match('OP'):
            op_node = Node(self.previous().value)
            right = self.term()
            op_node.add_child(left)
            op_node.add_child(right)
            left = op_node
        return left

    def term(self):
        if self.match('NUMBER'):
            return Node(self.previous().value)
        elif self.match('ID'):
            return Node(self.previous().value)
        self.error("Expected a number or identifier")

    def match(self, token_type):
        if self.pos < len(self.tokens) and self.tokens[self.pos].token_type == token_type:
            self.pos += 1
            return True
        return False

    def consume(self, token_type):
        if not self.match(token_type):
            self.error(f"Expected token {token_type}")

    def previous(self):
        return self.tokens[self.pos - 1]

    def error(self, message):
        token = self.tokens[self.pos] if self.pos < len(self.tokens) else self.previous()
        raise SyntaxError(f"{message} at line {token.line}")

# Función principal para ejecutar el análisis léxico y sintáctico
def main():
    print("Introduce el código (presiona Enter después de cada línea para tokenizar):")
    
    source_code = ''
    
    while True:
        line = input()
        if line.strip() == '':
            break
        source_code += line + '\n'
        
        # Tokenizar y mostrar los resultados inmediatamente
        lexer = Lexer(source_code)
        try:
            tokens = lexer.tokenize()
            # Imprimir los tokens resultantes
            for token in tokens:
                print(token)
            
            # Analizar y mostrar el árbol sintáctico
            parser = Parser(tokens)
            ast = parser.parse()
            print("\nÁrbol sintáctico:")
            for node in ast:
                print(node)

        except SyntaxError as e:
            print(e)

if __name__ == "__main__":
    main()
