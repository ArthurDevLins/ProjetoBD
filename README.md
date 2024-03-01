import psycopg2

class BancoDeDados:
    def __init__(self, dbname='ucsal_db', user='postgres', password='1234', host='localhost', port='5432'):
        self.conn = psycopg2.connect(dbname=dbname, user=user, password=password, host=host, port=port)
        self.cursor = self.conn.cursor()
        self.criar_tabela()

    def criar_tabela(self):
        self.cursor.execute('''
             CREATE TABLE IF NOT EXISTS consumidor (
                id SERIAL PRIMARY KEY,
                nome VARCHAR(255) NOT NULL,
                gmail VARCHAR(255) NOT NULL,
                cidade VARCHAR(100) NOT NULL,
               	idade INTEGER NOT NULL
            )
        ''')
        self.conn.commit()

    def inserir_consumidor(self, nome, gmail, cidade, idade):
        try:
            self.cursor.execute('''
                INSERT INTO consumidor (nome, gmail, cidade, idade)
                VALUES (%s, %s, %s, %s)
            ''', (nome, gmail, cidade, idade))
            self.conn.commit()
            print('Consumidor cadastrado com sucesso.')
        except Exception as e:
            print('Erro ao inserir consumidor:', e)

    def listar_consumidores(self):
        self.cursor.execute('SELECT * FROM consumidor')
        consumidores = self.cursor.fetchall()
        if not consumidores:
            print('Nenhum consumidor encontrado.')
        else:
            for consumidor in consumidores:
                print(consumidor)

    def atualizar_consumidor(self, consumidor_id, nome, gmail, cidade, idade):
        try:
            self.cursor.execute('''
                UPDATE consumidor
                SET nome=%s, gmail=%s, cidade=%s, idade=%s
                WHERE id=%s
            ''', (nome, gmail, cidade, idade, consumidor_id))
            self.conn.commit()
            print('Consumidor atualizado com sucesso.')
        except Exception as e:
            print('Erro ao atualizar consumidor:', e)

    def excluir_consumidor(self, consumidor_id):
        try:
            self.cursor.execute('DELETE FROM consumidor WHERE id=%s', (consumidor_id,))
            self.conn.commit()
            print('Consumidor excluído com sucesso.')
        except Exception as e:
            print('Erro ao excluir consumidor:', e)

    def __del__(self):
        self.conn.close()


if __name__ == '__main__':
    banco = BancoDeDados()

    while True:
        print('\n### Menu ###')
        print('1. Inserir Consumidor')
        print('2. Listar Consumidor')
        print('3. Atualizar Consumidor')
        print('4. Excluir Consumidor')
        print('5. Sair')
        escolha = input('Escolha uma opção: ')

        if escolha == '1':
            nome = input('Digite o nome do consumidor: ')
            gmail = input('Digite o gmail do consumidor: ')
            cidade = input('Digite a cidade do consumidor: ')
            try:
                idade = int(input('Digite a idade do consumidor: '))
                banco.inserir_consumidor(nome, gmail, cidade, idade)
            except ValueError:
                print('Erro: Idade deve ser um número inteiro.')
        elif escolha == '2':
            print('\nLista de consumidores:')
            banco.listar_consumidor()
        elif escolha == '3':
            try:
                consumidor_id = int(input('Digite o ID do consumidor que deseja atualizar: '))
                nome = input('Digite o novo nome do consumidor: ')
                gmail = input('Digite o nova gmail do consumidor: ')
                cidade = input('Digite a nova cidade do consumidor: ')
                idade = int(input('Digite a nova idade consumidor: '))
                banco.atualizar_consumidor(consumidor_id, nome, gmail, cidade, idade)
            except ValueError:
                print('Erro: ID e idade devem ser números inteiros.')
        elif escolha == '4':
            try:
                consumidor_id = int(input('Digite o ID do consumidor que deseja excluir: '))
                banco.excluir_consumidor(consumidor_id)
            except ValueError:
                print('Erro: ID do consumidor deve ser um número inteiro.')
        elif escolha == '5':
            print('Saindo do programa. Até mais!')
            break
        else:
            print('Opção inválida. Tente novamente.')
