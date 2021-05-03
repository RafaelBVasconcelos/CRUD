# CRUD
CRUD de Vendas criado em console para teste e aperfeiçoamento de linguagem,


using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;


namespace crud_VENDAS

{
    class Program
    {

        static void executaSQL(string sql)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand(sql, con);
            cmd.ExecuteNonQuery();
            con.Close();
        }
        static DataTable verprod(string tabela)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("select CodB as 'Código de Barras', NmP as 'Nome do Produto', Valor, Quant as 'Quantidade' from " + tabela, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dt = new DataTable();
            da.Fill(dt);
            con.Close();

            return dt;
        }

        static DataTable CalcVl(int cod, int quant)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("select sum (valor *" + quant + "  ) as 'Valor do orçamento R$' from estoquecompra where codb =" + cod, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtvl = new DataTable();
            da.Fill(dtvl);
            con.Close();

            return dtvl;
        }

        static DataTable Calcqt(int cod)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("select quant as 'Quantidade disponível' from estoquecompra where codb =" + cod, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtq = new DataTable();
            da.Fill(dtq);
            con.Close();

            return dtq;
        }

        static DataTable updatetbvl(int quant, int cod, int upvd)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("update estoquecompra set quant= " + quant + " where codb =" + cod + " \n" + "update estoquevenda set quant = " + upvd + " where codb =" + cod, con); //codigo sql
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtup = new DataTable();
            da.Fill(dtup);
            con.Close();

            return dtup;
        }
        static DataTable atestq(int quant, int cod)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("update estoquecompra set quant= " + quant + " + (select quant from estoquecompra where codb =" + cod + ") " + "where codb =" + cod, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtat = new DataTable();
            da.Fill(dtat);
            con.Close();

            return dtat;
        }

        static DataTable Del(int cod)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("delete estoquecompra where codb =" + cod, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtdel = new DataTable();
            da.Fill(dtdel);
            con.Close();
            return dtdel;
        }

        static DataTable estqbx(int quant)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("select CodB as 'Código de Barras', NmP as 'Nome do Produto', Valor, Quant as 'Quantidade' from  estoquecompra where quant <= " + quant, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtbx = new DataTable();
            da.Fill(dtbx);
            con.Close();

            return dtbx;
        }
        static DataTable estvd(string tabela)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("select CodB as 'Código de Barras', NmP as 'Nome do Produto', Valor, Quant as 'Quantidade' from " + tabela, con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtvd = new DataTable();
            da.Fill(dtvd);
            con.Close();

            return dtvd;
        }
        static DataTable nvprd(int cdb, string nmp, double vlr, int nq)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            con.Open();
            SqlCommand cmd = new SqlCommand("insert into EstoqueCompra (codb,nmp,valor,quant) values (" + cdb + ",'" + nmp + "'," + vlr + "," + nq + ")" + "\ninsert into Estoquevenda(codb, nmp, valor, quant) values(" + cdb + ", '" + nmp + "', " + vlr + "," + 0 + ")", con);
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dtnvp = new DataTable();
            da.Fill(dtnvp);
            con.Close();

            return dtnvp;
        }
        static void Main(string[] args)
        {
            SqlConnection con = new SqlConnection("Data Source=Rafael-PC\\RPC;Initial Catalog=TrabFinal;Integrated Security=True");
            int codb, q;
            int op;

            do
            {
                Console.WriteLine("\nDigite \n1 para fazer um orçamento e venda \n2 para atualizar o estoque \n3 para excluir um produto do estoque disponível \n4 para visualizar os produtos em baixa no estoque \n5 para visualizar o registro de vendas \n6 para inserir um  novo produto \n0 PARA SAIR");
                op = Convert.ToInt32(Console.ReadLine());

                if (op == 1)
                {

                    DataTable dt = verprod("estoquecompra");

                    for (int i = 0; i < dt.Rows.Count; i++)
                    {
                        for (int j = 0; j < dt.Columns.Count; j++)
                        {

                            Console.Write(dt.Columns[j].ToString() + " --> ");
                            Console.Write(dt.Rows[i][j].ToString() + "\n");

                        }

                    }


                    Console.WriteLine("\nDigite o código do produto:");
                    codb = Convert.ToInt32(Console.ReadLine());
                    Console.WriteLine("\nDigite a quantidade:");
                    q = Convert.ToInt32(Console.ReadLine());
                    DataTable dtvl = CalcVl(codb, q);



                    DataTable dtq = Calcqt(codb);
                    int qtd = Convert.ToInt32(dtq.Rows[0][0].ToString());
                    if (q > qtd)
                    {
                        Console.WriteLine("Não existe quantidade disponível no estoque.");

                    }

                    else
                    {
                        Console.Write(dtvl.Columns[0].ToString() + " ");
                        Console.Write(dtvl.Rows[0][0].ToString() + "\n");
                        Console.WriteLine("Existem " + qtd + " produtos disponíveis no estoque.");
                        Console.WriteLine("Deseja continuar a venda? 1 para sim 2 para não");
                        op = Convert.ToInt32(Console.ReadLine());

                        double vl = Convert.ToDouble(dtvl.Rows[0][0].ToString());
                        if (op == 1)

                        {
                            Console.WriteLine("Qual a forma de pagamento?");
                            Console.WriteLine("1 para dinheiro, 2 para débito, 3 para outros");
                            op = Convert.ToInt32(Console.ReadLine());
                            if (op == 1)

                            {


                                double dc = vl / 100 * 5;
                                Console.WriteLine("Seu desconto é de R$" + dc);

                                Console.WriteLine("O valor total a pagar é de R$" + (vl - dc));
                                Console.WriteLine("A compra foi finalizada? 1 para sim 2 para não");
                                op = Convert.ToInt32(Console.ReadLine());
                                if (op == 1)

                                {

                                    qtd -= q;
                                    DataTable dtup = updatetbvl(qtd, codb, q);
                                    Console.WriteLine("Compra realizada com sucesso!");

                                    Console.WriteLine("Estoque do produto " + codb + " atualizado para " + qtd);

                                }

                            }
                            else if (op == 2)
                            {
                                double dc = vl / 100 * 2;
                                Console.WriteLine("Seu desconto é de R$" + dc);
                                Console.WriteLine("O valor total a pagar é de R$" + (vl - dc));
                                Console.WriteLine("A compra foi finalizada? 1 para sim 2 para não");
                                op = Convert.ToInt32(Console.ReadLine());
                                if (op == 1)

                                {

                                    qtd -= q;
                                    DataTable dtup = updatetbvl(qtd, codb, q);
                                    Console.WriteLine("Compra realizada com sucesso!");

                                    Console.WriteLine("Estoque do produto " + codb + " atualizado para " + qtd);

                                }

                            }
                            else if (op == 3)
                            {
                                Console.Write("Valor a pagar R$");
                                Console.Write(dtvl.Rows[0][0].ToString() + "\n");
                                Console.WriteLine("A compra foi finalizada? 1 para sim 2 para não");
                                op = Convert.ToInt32(Console.ReadLine());
                                if (op == 1)

                                {
                                    qtd -= q;
                                    DataTable dtup = updatetbvl(qtd, codb, q);
                                    Console.WriteLine("Compra realizada com sucesso!");

                                    Console.WriteLine("Estoque do produto " + codb + " atualizado para " + qtd + " unidades.");

                                }

                            }

                        }
                    }
                }
                else if (op == 2)
                {
                    Console.WriteLine("Digite o código do produto e a quantidade adquirida");
                    codb = Convert.ToInt32(Console.ReadLine());
                    q = Convert.ToInt32(Console.ReadLine());
                    DataTable dtat = atestq(q, codb);
                    Console.WriteLine("Quantidade atualizada com sucesso!\nVocê adicionou " + q + " unidades ao produto " + codb);

                }
                else if (op == 3)
                {
                    Console.WriteLine("Digite o código do produto que será excluído do estoque disponível");
                    codb = Convert.ToInt32(Console.ReadLine());
                    Console.WriteLine("Produto " + codb + " exclúido.");
                    Console.WriteLine("Deseja desfazer essa ação? 1 para sim 2 para não");
                    op = Convert.ToInt32(Console.ReadLine());
                    if (op == 1)

                    {
                        Console.WriteLine("O produto não foi excluido");
                    }

                    else if (op == 2)

                    {
                        DataTable dtdel = Del(codb);
                    }

                }

                else if (op == 4)

                {
                    Console.WriteLine("Os produtos exibidos devem estar abaixo de qual quantidade?");
                    q = Convert.ToInt32(Console.ReadLine());
                    DataTable dtbx = estqbx(q);
                    for (int i = 0; i < dtbx.Rows.Count; i++)
                    {
                        for (int j = 0; j < dtbx.Columns.Count; j++)
                        {

                            Console.Write(dtbx.Columns[j].ToString() + " --> ");
                            Console.Write(dtbx.Rows[i][j].ToString() + "\n");
                        }

                    }

                }
                else if (op == 5)

                {
                    DataTable dtvd = estvd("estoquevenda");

                    for (int i = 0; i < dtvd.Rows.Count; i++)
                        for (int j = 0; j < dtvd.Columns.Count; j++)
                        {

                            Console.Write(dtvd.Columns[j].ToString() + "\n");
                            Console.Write(dtvd.Rows[i][j].ToString() + "\n");
                        }

                }
                else if (op == 6)
                {
                    Console.WriteLine("Digite o código do no novo produto");
                    codb = Convert.ToInt32(Console.ReadLine());
                    Console.WriteLine("Digite o nome do novo produto:");
                    string nmp = Convert.ToString(Console.ReadLine());
                    Console.WriteLine("Digite o valor do novo produto:");
                    double vlr = Convert.ToDouble(Console.ReadLine());
                    Console.WriteLine("Digite a quantidade do novo produto:");
                    int nvq = Convert.ToInt32(Console.ReadLine());
                    DataTable dtnvp = nvprd(codb, nmp, vlr, nvq);
                    Console.WriteLine("\nO produto foi adicionado com sucesso.\n");
                }
                else if (op > 6 || op < 0)
                {
                    Console.WriteLine("\nOpção inválida.");
                }


            }
            while (op != 0);
        }





    }


}
