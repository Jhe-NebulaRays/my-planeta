# my-planeta
My clone repository
import 'package:flutter/material.dart';
import 'package:sqflite/sqflite.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await criarBancoDeDados();
  runApp(MeuAplicativo());
}

class MeuAplicativo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Gerenciador de Planetas',
      tema: ThemeData(
        corPrimária: Colors.blue,
      ),
      home: TelaInicial(),
    );
  }
}

class TelaInicial extends StatefulWidget {
  @override
  _TelaInicialState createState() => _TelaInicialState();
}

class _TelaInicialState extends State<TelaInicial> {
  List<Planeta> planetas = [];

  @override
  void initState() {
    super.initState();
    carregarPlanetas();
  }

  Future<void> carregarPlanetas() async {
    final database = await abrirBancoDeDados();
    final List<Map<String, dynamic>> resultado = await database.query('planetas');
    setState(() {
      planetas = resultado.map((map) => Planeta.fromMap(map)).toList();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        título: Text('Gerenciador de Planetas'),
      ),
      corpo: ListView.builder(
        itemCount: planetas.length,
        itemBuilder: (context, index) {
          return ListTile(
            título: Text(planetas[index].nome),
            subtítulo: Text(planetas[index].apelido),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => TelaDetalhes(planeta: planetas[index])),
              );
            },
          );
        },
      ),
      botãoFlutuante: FloatingActionButton(
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => TelaCadastro()),
          );
        },
        dica: 'Adicionar planeta',
        filho: Icon(Icons.add),
      ),
    );
  }
}

class TelaDetalhes extends StatelessWidget {
  final Planeta planeta;

  TelaDetalhes({required this.planeta});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        título: Text(planeta.nome),
      ),
      corpo: Padding(
        preenchimento: const EdgeInsets.all(16.0),
        filho: Column(
          filhos: [
            Text('Apelido: ${planeta.apelido}'),
            Text('Distância do sol: ${planeta.distanciaDoSol} UA'),
            Text('Tamanho: ${planeta.tamanho} km'),
          ],
        ),
      ),
    );
  }
}

class TelaCadastro extends StatefulWidget {
  @override
  _TelaCadastroState createState() => _TelaCadastroState();
}

class _TelaCadastroState extends State<TelaCadastro> {
  final _formKey = GlobalKey<FormState>();
  final _nomeController = TextEditingController();
  final _apelidoController = TextEditingController();
  final _distanciaDoSolController = TextEditingController();
  final _tamanhoController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        título: Text('Cadastrar planeta'),
      ),
      corpo: Padding(
        preenchimento: const EdgeInsets.all(16.0),
        filho: Form(
          chave: _formKey,
          filho: Column(
            filhos: [
              TextFormField(
                controlador: _nomeController,
                decoração: InputDecoration(rótuloDeTexto: 'Nome do planeta'),
                validador: (valor) {
                  if (valor!.isEmpty) {
                    return 'Por favor, informe o nome do planeta';
                  }
                  return null;
                },
              ),
              TextFormField(
                controlador: _apelidoController,
                decoração: InputDecoration(rótuloDeTexto: 'Apelido'),
              ),
              TextFormField(
                controlador: _distanciaDoSolController,
                decoração: InputDecoration(rótuloDeTexto: 'Distância do sol (UA)'),
                validador: (valor) {
                  if (valor!.isEmpty) {
                    return 'Por favor, informe a distância do sol';
                  }
                  if (double.tryParse(valor) == null) {
                    return 'Por favor, informe um valor numérico';
                  }
                  return null;
                },
              ),
              TextFormField(
                controlador: _tamanhoController,
                decoração: InputDecoration(rótuloDeTexto: 'Tamanho (km)'),
                validador: (valor) {
                  if (valor!.isEmpty) {
                    return 'Por favor, informe o tamanho';
                  }
                  if (double.tryParse(valor) == null