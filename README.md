import 'package:sqflite/sqflite.dart';
Future< Database> criarBancoDeDados() async {
  String caminho = join(await getDatabasesPath(), 'planetas.db');
  return await openDatabase(caminho, version: 1, onCreate: (db, versao) {
    return db.execute('''
      CREATE TABLE planetas (
        id INTEGER PRIMARY KEY,
        nome TEXT NOT NULL,
        apelido TEXT,
        distancia_do_sol REAL NOT NULL,
        tamanho REAL NOT NULL
      )
    ''');
  });
}
class _TelaCadastroState extends State< TelaCadastro> {
  // ...
 Future< void> adicionarPlaneta() async {
    final database = await criarBancoDeDados();
    final planeta = Planeta(
      nome: _nomeController.text,
      apelido: _apelidoController.text,
      distanciaDoSol: double.parse(_distanciaDoSolController.text),
      tamanho: double.parse(_tamanhoController.text),
    );
    await database.insert('planetas', planeta.toMap());
    Navigator.pop(context);
  }
Future< void> removerPlaneta(Planeta planeta) async {
    final database = await criarBancoDeDados();
    await database.delete('planetas', where: 'id = ?', whereArgs: [ planeta.id]);
    Navigator.pop(context);
  }
Future< void> atualizarPlaneta(Planeta planeta) async {
    final database = await criarBancoDeDados();
    await database.update('planetas', planeta.toMap(), where: 'id = ?', whereArgs: [ planeta.id]);
    Navigator.pop(context);
  }
}
class _TelaInicialState extends State< TelaInicial> {
  // ...
Future< void> carregarPlanetas() async {
    final database = await criarBancoDeDados();
    final List< Map< String, dynamic>> resultado = await database.query('planetas');
    setState(() {
      planetas = resultado.map((map) => Planeta.fromMap(map)).toList();
    });
  }
 @override
  Widget build(BuildContext context) {
    return Scaffold(
      // ...
      corpo: ListView.builder(
        itemCount: planetas.length,
        itemBuilder: (context, index) {
          return ListTile(
            título: Text(planetas[ index].nome),
            subtítulo: Text(planetas[ index].apelido),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => TelaDetalhes(planeta: planetas[ index])),
              );
            },
          );
        },
      ),
      // ...
    );
  }
}ass Planeta {
  int id;
  String nome;
  String apelido;
  double distanciaDoSol;
  double tamanho;
Planeta({this.id, this.nome, this.apelido, this.distanciaDoSol, this.tamanho});
factory Planeta.fromMap(Map< String,dynamic> map) {
    return Planeta(
      id: map[' id'],
      nome: map[' nome'],
      apelido: map[' apelido'],
      distanciaDoSol: map[' distancia_do_sol'],
      tamanho: map[' tlamanho '],
    );
  }
}
