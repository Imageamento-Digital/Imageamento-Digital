# Imageamento Digital
import PySimpleGUI as sg
import cv2 as cv

layout = [[sg.Text('Insira a imagem para realizar o Teste de Chagas:')],
          [sg.Input(), sg.FileBrowse(key='open')],
          [sg.Text('OBS: Nenhuma pasta ao longo do caminho pode ter caracteres especiais (incluindo espaços)')],
          [sg.Text('Exemplo de caminho válido: C:\programas\Boreas\chagas.jpg')],
          [sg.OK(), sg.Cancel()]]

window = sg.Window('Abrir imagem para Teste de Chagas', layout)

event, values = window.Read()
imagem = values['open']
window.close()
img = cv.imread(f'{imagem}')

# print(img.shape)
cropped = img[203:297, 800:1050]
# cv.imshow("Imagem cortada", cropped)
b, g, r = cv.split(cropped)
# print(cropped.shape)
cont = 0
for i in range(2, 94):
    for j in range(2, 250):
        if (int(b[i][j]) + int(g[i][j]) < int(r[i][j]) + 8 or int(b[i][j]) + int(g[i][j]) + int(r[i][j]) < 10) and int(r[i][j]) < 130:
            b[i][j] = 0
            g[i][j] = 0
            r[i][j] = 255
            cont = cont + 1


croppednew = cv.merge([b, g, r])
# hist = cv.calcHist([cropped], [2], None, [256], [0, 256])
# plt.plot(hist)


class TelaPython:

    def __init__(self):
        # layout
        layout = [
            [sg.Text('Digite o nome do paciente', size=(20, 0)), sg.Input(size=(20, 0), key='nome')],
            [sg.Text('Deseja ver o resultado do Teste de Chagas?')],
            [sg.Radio('Sim', 'grupox', key='Sim'), sg.Radio('Não', 'grupox', key='Não')],
            [sg.Text('Deseja visualizar as imagens com as seleções feitas?')],
            [sg.Radio('Sim', 'grupoy', key='Sim1'), sg.Radio('Não', 'grupoy', key='Não1')],
            [sg.Text('Deseja saber a quantidade de pixels relevantes para o teste de chagas na imagem?')],
            [sg.Radio('Sim', 'grupoz', key='Sim2'), sg.Radio('Não', 'grupoz', key='Não2')],
            [sg.Button('Enviar')],
            [sg.Output(size=(60, 10))]
        ]
        # Janela
        self.janela = sg.Window("Resultados da Análise").layout(layout)

    def Iniciar(self):
        while True:
            self.buttom, self.values = self.janela.Read()
            nome = self.values['nome']
            sim = self.values['Sim']
            nao = self.values['Não']
            sim1 = self.values['Sim1']
            sim2 = self.values['Sim2']
            print(f'Nome do paciente: {nome}')
            if sim == 1:
                if cont > 2100:
                    print("Resultado do Teste: Positivo")
                else:
                    print("Resultado do Teste: Negativo")
            if nao == 1:
                print("Resultado arquivado")
            if sim1 ==1:
                cv.imshow("Area selecionada", croppednew)
            if sim2 == 1:
                print(f"O numero de pixels em destaque na imagem é de {cont}")


tela = TelaPython()
tela.Iniciar()

