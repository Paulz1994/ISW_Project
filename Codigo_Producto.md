
# clscliente.cs

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.SqlClient;
using System.Data;

namespace MiAplicacion
{
    public class clscliente : Clsconexion
    {
        private string tabla = "cliente";
        protected string nombre, direccion, telefono;
        protected int idcliente;
        public clscliente(int idcliente, string nombre, string direccion, string telefono)
        {
            this.Idcliente = idcliente;
            this.nombre = nombre;
            this.direccion = direccion;
            this.telefono = telefono;
        }
        public int Idcliente
        {
            set { idcliente = value; }
            get { return idcliente; }
        }
        public String Nombre
        {
            set { nombre = value; }
            get { return nombre; }
        }
        public String Direccion
        {
            set { direccion = value; }
            get { return direccion; }
        }
        public String Telefono
        {
            set { telefono = value; }
            get { return telefono; }
        }

        //método agregar registro cliente
        public void agregar()
        {
            conectar(tabla);
            DataRow fila;
            fila = Data.Tables[tabla].NewRow();
            fila["idcliente"] = Idcliente;
            fila["nombre"] = Nombre;
            fila["direccion"] = Direccion;
            fila["telefono"] = Telefono;

            Data.Tables[tabla].Rows.Add(fila);
            AdaptadorDatos.Update(Data, tabla);
        }

        public bool eliminar(int valor)
        {
            conectar(tabla);
            DataRow fila;
            int x = Data.Tables[tabla].Rows.Count - 1;
            for (int i = 0; i <= x; i++)
            {
                fila = Data.Tables[tabla].Rows[i];

                if (int.Parse(fila["idcliente"].ToString()) == valor)
                {
                    fila = Data.Tables[tabla].Rows[i];
                    fila.Delete();
                    DataTable tablaborrados;
                    tablaborrados = Data.Tables[tabla].GetChanges(DataRowState.Deleted);
                    AdaptadorDatos.Update(tablaborrados);
                    Data.Tables[tabla].AcceptChanges();
                    return true;

                }

            }
            return false;
        }

        public bool existe(int valor)
        {
            conectar(tabla);
            DataRow fila;
            int x = Data.Tables[tabla].Rows.Count - 1;
            for (int i = 0; i <= x; i++)
            {
                fila = Data.Tables[tabla].Rows[i];

                if (int.Parse(fila["idcliente"].ToString()) == valor)
                {
                    Idcliente = int.Parse(fila["idcliente"].ToString());
                    Nombre = fila["nombre"].ToString();
                    Direccion = fila["direccion"].ToString();
                    Telefono = fila["telefono"].ToString();
                    return true;
                }
            }return false;
        }
    }
}

```

# clsconexion.cs

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;

namespace MiAplicacion

{
    public class Clsconexion
    {
        protected SqlDataReader reader;
        protected SqlDataAdapter AdaptadorDatos;
        protected DataSet data;
        protected SqlConnection oconexion = new SqlConnection();

        public Clsconexion()
        {

        }
        public void conectar(string tabla)
        {
            string strConeccion = ConfigurationManager.ConnectionStrings["clientesConnectionString"].ConnectionString;
            oconexion.ConnectionString = strConeccion;
            oconexion.Open();
            AdaptadorDatos = new SqlDataAdapter("select * from " + tabla, oconexion);
            SqlCommandBuilder ejecutacomandos = new SqlCommandBuilder(AdaptadorDatos);
            Data = new DataSet();

            AdaptadorDatos.Fill(Data, tabla);
            oconexion.Close();  
        }

        public DataSet Data{
            set { data = value; }   
            get { return data; }    
        }
        public SqlDataReader DataReader{
            set { reader = value; }
            get { return  reader; }
        }
    }

    
}

```

# pagecliente.aspx.cs

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

namespace MiAplicacion
{
    public partial class Formulario_web1 : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void btnagregar_Click(object sender, EventArgs e)
        {
            try
            {
                clscliente clte = new clscliente(0, "", "", "");
                clte.Idcliente = int.Parse(txtcliente.Text);
                clte.Nombre = txtnombre.Text;
                clte.Direccion = txtdireccion.Text;
                clte.Telefono = txttelefono.Text;
                clte.agregar();
                lblestado.Text = "Registro agregado con exito";
                txtcliente.Text = "";
                txtnombre.Text = "";
                txtdireccion.Text = "";
                txttelefono.Text = "";
            }
            catch
            {
                lblestado.Text = "Se ha generado una excepción";
            }
        }

        protected void btneliminar_Click(object sender, EventArgs e)
        {
            clscliente clte = new clscliente(0, "", "", "");
            if (clte.eliminar(int.Parse(txtcliente.Text)))
            {
                lblestado.Text = "El registro se eliminó con éxito";
                txtcliente.Text = "";
                txtnombre.Text = "";
                txtdireccion.Text = "";
                txttelefono.Text = "";
            }
            else { lblestado.Text = "El registro no se eliminó"; }

        }

        protected void btnbuscar_Click(object sender, EventArgs e)
        {
            clscliente clte = new clscliente(0, "", "", "");
            if (clte.existe(int.Parse(txtcliente.Text)))
            {
                txtcliente.Text = clte.Idcliente.ToString();
                txtdireccion.Text = clte.Direccion;
                txtnombre.Text = clte.Nombre;
                txttelefono.Text =clte.Telefono;    
                lblestado.Text = "Registro encontrado";
            }
            else
            {
                lblestado.Text = "Registro no encontrado";
            }
        }
    }
}

```

# Masterpage.Master

```C#
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<script runat="server">

    Protected Sub Page_Load(sender As Object, e As EventArgs)

    End Sub
</script>

<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<title>Página Maestra</title>
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1" />
<link rel="stylesheet" href="layout/styles/layout.css" type="text/css" />
<script type="text/javascript" src="layout/scripts/jquery.min.js"></script>
<script type="text/javascript" src="layout/scripts/jquery.jcarousel.pack.js"></script>
<script type="text/javascript" src="layout/scripts/jquery.jcarousel.setup.js"></script>
    <style type="text/css">
        .auto-style2 {
            left: 0px;
            top: 0px;
            width: 848px;
        }
        .auto-style3 {
            width: 312px;
            height: 154px;
        }
        .auto-style4 {
            width: 100%;
        }
        .auto-style5 {
            width: 1575px;
            height: 787px;
        }
    </style>
    </head>
<body id="top">
    <form id="form1" runat="server">
<!-- ####################################################################################################### -->
<div class="wrapper col1">
  <div id="header" class="auto-style2">
    <div id="topnav"><!-- menu -->
      <ul>
        <li class="active"><a href="default.aspx">Inicio</a></li>
        <li><a href="pagecliente.aspx">Clientes</a></li>
        <li><a href="pages/full-width.html">Proveedores</a></li>
        <li><a href="#">Almacen</a>
          <ul>
            <li><a href="#">Artículos</a></li>
            <li><a href="#">Inventario</a></li>
            <li><a href="#">Catálogos</a></li>
          </ul>
        </li>
        <li class="last"><a href="#">Consultas</a></li>
      </ul>
    </div>
      <img alt="LOGO" class="auto-style3" src="file:///C:/Users/Víctor/source/repos/MiAplicacion/MiAplicacion/optica.jpeg" /><br class="clConsultas</a></li>
        </ul>
    </div>
    <br class="clear" />
  </div>
</div>
<!-- ####################################################################################################### -->
<div class="wrapper col3">

    <asp:ContentPlaceHolder ID="ContentPlaceHolder1" runat="server">
    </asp:ContentPlaceHolder>

    </div>
<!-- ####################################################################################################### -->
<div class="wrapper col5">
  <div id="copyright">
    <p class="fl_left">Copyright &copy; 2022 - All Rights Reserved - <a href="#">VAFV</a></p>
    <br class="clear" />
  </div>
</div>
    </form>
    &nbsp;
    <table class="auto-style4">
        <tr>
            <td>&nbsp;</td>
        </tr>
    </table>
    <p>
        <img alt="fondo" class="auto-style5" src="file:///C:/Users/Víctor/source/repos/MiAplicacion/MiAplicacion/opticafondo.jpg" /></p>
</body>
</html>

```

# MasterPage.Master.cs

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

namespace MiAplicacion
{
    public partial class MasterPage : System.Web.UI.MasterPage
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }
    }
}
```

# MasterPage.Master.designer.cs

```C#
namespace MiAplicacion
{


    public partial class MasterPage
    {

        /// <summary>
        /// control form1.
        /// </summary>
        /// <remarks>
        /// Campo generado automáticamente.
        /// Para modificarlo, mueva la declaración del campo del archivo del diseñador al archivo de código subyacente.
        /// </remarks>
        protected global::System.Web.UI.HtmlControls.HtmlForm form1;

        /// <summary>
        /// Control ContentPlaceHolder1.
        /// </summary>
        /// <remarks>
        /// Campo generado automáticamente.
        /// Para modificarlo, mueva la declaración del campo del archivo del diseñador al archivo de código subyacente.
        /// </remarks>
        protected global::System.Web.UI.WebControls.ContentPlaceHolder ContentPlaceHolder1;
    }
}
```

# PageCliente.aspx

```C#
<%@ Page Title="" Language="C#" MasterPageFile="~/MasterPage.Master" AutoEventWireup="true" CodeBehind="pagecliente.aspx.cs" Inherits="MiAplicacion.Formulario_web1" %>
<asp:Content ID="Content1" ContentPlaceHolderID="ContentPlaceHolder1" runat="server">
    <table style="width: 100%">
        <tr>
            <td colspan="3"><h1> Clientes
                            </h1></td>
        </tr>
        <tr>
            <td style="width: 143px;" rowspan="5">
                <img alt="cliente" src="file:///C:/Users/Víctor/source/repos/MiAplicacion/MiAplicacion/cliente3.jpg" style="width: 222px; height: 195px" /><asp:Label ID="lblestado" runat="server" ForeColor="#CC3300"></asp:Label>
            </td>
            <td style="height: 26px; width: 210px;">
                <asp:Label ID="Label1" runat="server" Text="Id Cliente"></asp:Label>
            </td>
            <td style="height: 26px">
                <asp:TextBox ID="txtcliente" runat="server"></asp:TextBox>
                <asp:Button ID="btnbuscar" runat="server" OnClick="btnbuscar_Click" Text="Buscar" />
            </td>
        </tr>
        <tr>
            <td style="width: 210px">
                <asp:Label ID="Label2" runat="server" Text="Nombre"></asp:Label>
            </td>
            <td>
                <asp:TextBox ID="txtnombre" runat="server"></asp:TextBox>
            </td>
        </tr>
        <tr>
            <td style="width: 210px">
                <asp:Label ID="Label3" runat="server" Text="Dirección "></asp:Label>
            </td>
            <td>
                <asp:TextBox ID="txtdireccion" runat="server"></asp:TextBox>
            </td>
        </tr>
        <tr>
            <td style="width: 210px">
                <asp:Label ID="Label4" runat="server" Text="Teléfono"></asp:Label>
            </td>
            <td>
                <asp:TextBox ID="txttelefono" runat="server"></asp:TextBox>
            </td>
        </tr>
        <tr>
            <td style="width: 210px">&nbsp;</td>
            <td>
                <asp:Button ID="btnagregar" runat="server" Text="Agregar" OnClick="btnagregar_Click" />
                <asp:Button ID="btneliminar" runat="server" OnClick="btneliminar_Click" style="height: 22px" Text="Eliminar" />
            </td>
        </tr>
               </table>
</asp:Content>
```

# web.config

```C#
<?xml version="1.0" encoding="utf-8"?>
<!--
  Para obtener más información sobre cómo configurar la aplicación ASP.NET, visite
  https://go.microsoft.com/fwlink/?LinkId=169433
  -->
<configuration>
  <connectionStrings>
    <add name="clientesConnectionString" connectionString="Data Source=VICTOR\VAFV;Initial Catalog=clientes;User ID=sa;Password=1234"
      providerName="System.Data.SqlClient" />
  </connectionStrings>
  <system.web>
    <compilation debug="true" targetFramework="4.7.2" />
    <httpRuntime targetFramework="4.7.2" />
  </system.web>
  <system.codedom>
    <compilers>
      <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
      <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
    </compilers>
  </system.codedom>
</configuration>
```
