package escuelaa;

import java.awt.Color;
import java.awt.Component;
import java.awt.Cursor;
import java.awt.FlowLayout;
import java.awt.Font;
import java.awt.GridLayout;
import java.awt.Image;
import java.awt.Window;
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

import javax.swing.BorderFactory;
import javax.swing.Box;
import javax.swing.BoxLayout;
import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.JTextField;
import javax.swing.SwingConstants;
import javax.swing.SwingUtilities;
import javax.swing.UIManager;

public class EEscuela {

    private static final String ARCHIVO_CALIFICACIONES = "calificaciones.txt";

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            abrirMenuPrincipal();
        });
    }

    public static void abrirMenuPrincipal() {
        ImageIcon LogoEsc = new ImageIcon("C:\\Users\\Usuario\\Pictures\\escuela\\logoesc.png");
        Image logoEscalado = LogoEsc.getImage().getScaledInstance(100, 120, Image.SCALE_SMOOTH);
        JLabel etiquetaLogo = new JLabel(new ImageIcon(logoEscalado), SwingConstants.CENTER);
        
        ImageIcon icono1 = new ImageIcon("C:\\Users\\Usuario\\Pictures\\escuela\\estudiante.png");
        ImageIcon icono2 = new ImageIcon("C:\\Users\\Usuario\\Pictures\\escuela\\profesor.png");
        Image Estudiatesredimensionado = icono1.getImage().getScaledInstance(200, 200, Image.SCALE_SMOOTH);
        ImageIcon Estudiantes = new ImageIcon(Estudiatesredimensionado);
        
        Image Profesoresredimensionado = icono2.getImage().getScaledInstance(200, 200, Image.SCALE_SMOOTH);
        ImageIcon Profesores = new ImageIcon(Profesoresredimensionado);

        JButton botonA = new JButton(Estudiantes);
        JButton botonB = new JButton(Profesores);

        JLabel etiquetaBienvenidos = new JLabel("Bienvenid@", SwingConstants.CENTER);
        etiquetaBienvenidos.setFont(new Font(Font.SERIF, Font.BOLD, 18)); 
        etiquetaBienvenidos.setForeground(Color.WHITE);
        etiquetaBienvenidos.setOpaque(true); 
        etiquetaBienvenidos.setBackground(new Color(188, 138, 57)); 
        etiquetaBienvenidos.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10)); 
        
        UIManager.put("OptionPane.background", new Color(238, 224, 200));
        UIManager.put("Panel.background", new Color(247, 245, 235));
        UIManager.put("OptionPane.messageForeground", new Color(125, 92, 38));

        JPanel panelContenido = new JPanel();
        panelContenido.setLayout(new BoxLayout(panelContenido, BoxLayout.Y_AXIS));
        
        etiquetaLogo.setAlignmentX(Component.CENTER_ALIGNMENT);
        etiquetaBienvenidos.setAlignmentX(Component.CENTER_ALIGNMENT);
        
        panelContenido.add(etiquetaLogo);
        panelContenido.add(Box.createVerticalStrut(15)); 
        panelContenido.add(etiquetaBienvenidos);
        panelContenido.add(Box.createVerticalStrut(15));
        
        configurarBotonInvisible(botonA);
        configurarBotonInvisible(botonB);

        Object[] opcionesImagenes = { botonA, botonB };

        JPanel panelBotones = new JPanel(new FlowLayout(FlowLayout.CENTER, 25, 0));
        panelBotones.add(botonA);
        panelBotones.add(botonB);

        panelContenido.add(panelBotones);
        
        botonA.addActionListener(e -> {
            cerrarVentanaEmergente(botonA);
            abrirMenuA();
        });

        botonB.addActionListener(e -> {
            cerrarVentanaEmergente(botonB);
            abrirMenuB();
        });

        JOptionPane.showOptionDialog(
                null, 
                panelContenido,      
                "Escuela Libertad",    
                JOptionPane.DEFAULT_OPTION, 
                JOptionPane.PLAIN_MESSAGE, 
                null, 
                opcionesImagenes,     
                null
        );
    }

    public static void abrirMenuA() {
        int respuesta = JOptionPane.showConfirmDialog(null, 
                "Bienvenido al menú de estudiantes.", 
                "Estudiantes.", JOptionPane.YES_NO_OPTION);

        if (respuesta != JOptionPane.YES_OPTION) {
            abrirMenuPrincipal();
            return;
        }

        Map<String, String> baseDatos = obtenerBaseDeDatosUsuarios();

        JTextField cajaNombre = new JTextField(15);
        JPasswordField cajaContrasena = new JPasswordField(15);

        JPanel panelFormulario = new JPanel(new GridLayout(2, 2, 10, 10));
        panelFormulario.setBorder(BorderFactory.createEmptyBorder(15, 15, 15, 15));

        JLabel etiquetaNombre = new JLabel("Nombre de Usuario del Estudiante:");
        etiquetaNombre.setFont(new Font("Arial", Font.BOLD, 14));
        JLabel etiquetaContrasena = new JLabel("Contraseña:");
        etiquetaContrasena.setFont(new Font("Arial", Font.BOLD, 14));

        panelFormulario.add(etiquetaNombre);
        panelFormulario.add(cajaNombre);
        panelFormulario.add(etiquetaContrasena);
        panelFormulario.add(cajaContrasena);

        Object[] opcionesFormulario = {"Iniciar Sesión", "Regresar al Menú"};

        int seleccion = JOptionPane.showOptionDialog(
                null, 
                panelFormulario,               
                "Login - Estudiantes",     
                JOptionPane.DEFAULT_OPTION, 
                JOptionPane.PLAIN_MESSAGE, 
                null, 
                opcionesFormulario,            
                opcionesFormulario[0]
        );

        if (seleccion == 0) { 
            String usuarioIngresado = cajaNombre.getText().trim(); 
            String contrasenaIngresada = new String(cajaContrasena.getPassword());

            if (baseDatos.containsKey(usuarioIngresado)) {
                String contrasenaReal = baseDatos.get(usuarioIngresado);
                
                if (contrasenaReal.equals(contrasenaIngresada)) {
                    JOptionPane.showMessageDialog(null, "¡Bienvenido al sistema de Estudiantes, " + usuarioIngresado + "!", 
                            "Éxito", JOptionPane.INFORMATION_MESSAGE);
                    
                    Object[] opcionesEstudiante = {"Ver Calificación Final", "Cerrar Sesión"};
                    int seleccionEstudiante = JOptionPane.showOptionDialog(
                            null,
                            "¿Qué deseas hacer hoy, " + usuarioIngresado + "?",
                            "Panel del Estudiante",
                            JOptionPane.DEFAULT_OPTION,
                            JOptionPane.PLAIN_MESSAGE,
                            null,
                            opcionesEstudiante,
                            opcionesEstudiante[0]
                    );

                    if (seleccionEstudiante == 0) {

                        ArrayList<String[]> listaCalificaciones = cargarCalificacionesDesdeArchivo();
                        String notaEncontrada = null;

                        for (String[] registro : listaCalificaciones) {
                            
                            if (registro.length >= 2 && registro[0].equalsIgnoreCase(usuarioIngresado)) {
                                notaEncontrada = registro[1];
                                break;
                            }
                        }

                        if (notaEncontrada != null) {
                            JOptionPane.showMessageDialog(null, "Tu calificación asignada es: " + notaEncontrada,
                                    "Promedio Final del Curso", JOptionPane.INFORMATION_MESSAGE);
                        } else {
                            JOptionPane.showMessageDialog(null, "El profesor aún no ha subido tu calificación.",
                                    "Sin Asignar", JOptionPane.WARNING_MESSAGE);
                        }
                    }
                    abrirMenuPrincipal();
                } else {
                    JOptionPane.showMessageDialog(null, "Contraseña incorrecta. Inténtalo de nuevo.", 
                            "Error de Login", JOptionPane.ERROR_MESSAGE);
                    abrirMenuA(); 
                }
            } else {
                JOptionPane.showMessageDialog(null, "El usuario ingresado no existe.", 
                        "Error de Login", JOptionPane.ERROR_MESSAGE);
                abrirMenuA(); 
            }
        } else {
            abrirMenuPrincipal();
        }
    }

    private static Map<String, String> obtenerBaseDeDatosUsuarios() {
        Map<String, String> estudiantes = new HashMap<>();
        estudiantes.put("Angel Jonathan Donají Cruz", "LIBERTAD01");
        estudiantes.put("Joseline Yazmin Moreno Sosa", "LIBERTAD02");
        estudiantes.put("Rosario Adriana Santiago Garnica", "LIBERTAD03");
        estudiantes.put("Estrella Zarate Ramos", "LIBERTAD04");
        estudiantes.put("Leslie Alizee Vasquez Cruz", "LIBERTAD05");
        estudiantes.put("Ximena Miranda Navarro", "LIBERTAD06");
        estudiantes.put("Maximiliano Toleda Cruz", "LIBERTAD07");
        estudiantes.put("Josue Gonzales Cruz", "LIBERTAD08");
        estudiantes.put("Constanza Carolina Reyes Solis", "LIBERTAD09");
        estudiantes.put("Emmanuel Gutierrez Jarquín", "LIBERTAD10");
        estudiantes.put("Guadalupe Hernandez Ozorio", "LIBERTAD11");
        estudiantes.put("Miriam Ivette Ríos Garcia", "LIBERTAD12");
        estudiantes.put("Cristhian David Jarquín Hernandez", "LIBERTAD13");
        estudiantes.put("Victoria Altamirano Peralta", "LIBERTAD14");
        estudiantes.put("Julian Canalizo Mina", "LIBERTAD15");
        estudiantes.put("Carlos Enríquez Perez López", "LIBERTAD16");
        estudiantes.put("Sofia Bustamante Moral", "LIBERTAD17");
        estudiantes.put("Rosario Monsterrat Aquino Garnica", "LIBERTAD18");
        estudiantes.put("Jimena Concepción Solis Perez", "LIBERTAD19");
        estudiantes.put("Melanie Martinez Perez", "LIBERTAD20");
        estudiantes.put("Lizbeth Carolina Reyes", "LIBERTAD21");
        estudiantes.put("Ian Carlo Ríos Aragon", "LIBERTAD22");
        estudiantes.put("Danna Camila Ríos Carreño", "LIBERTAD23");
        estudiantes.put("Kevin Morales Aragosa", "LIBERTAD24");
        return estudiantes;
    }

    public static void abrirMenuB() {
        int respuesta2 = JOptionPane.showConfirmDialog(null, 
                "Bienvenido al menú de profesores", 
                "Profesores.", JOptionPane.YES_NO_OPTION);
        
        if (respuesta2 != JOptionPane.YES_OPTION) {
            abrirMenuPrincipal();
            return;
        }

        Map<String, String> profesores = obtenerBaseDeDatosPorfesores();

        JTextField cajaNombre = new JTextField(15);
        JPasswordField cajaContrasena = new JPasswordField(15);

        JPanel panelFormulario = new JPanel(new GridLayout(2, 2, 10, 10));
        panelFormulario.setBorder(BorderFactory.createEmptyBorder(15, 15, 15, 15));

        JLabel etiquetaNombre = new JLabel("Nombre de Usuario del Profesor:");
        etiquetaNombre.setFont(new Font("Arial", Font.BOLD, 14));
        JLabel etiquetaContrasena = new JLabel("Contraseña:");
        etiquetaContrasena.setFont(new Font("Arial", Font.BOLD, 14));

        panelFormulario.add(etiquetaNombre);
        panelFormulario.add(cajaNombre);
        panelFormulario.add(etiquetaContrasena);
        panelFormulario.add(cajaContrasena);

        Object[] opcionesFormulario = {"Iniciar Sesión", "Regresar al Menú"};

        int seleccion = JOptionPane.showOptionDialog(
                null, 
                panelFormulario,               
                "Login - Profesores",     
                JOptionPane.DEFAULT_OPTION, 
                JOptionPane.PLAIN_MESSAGE, 
                null, 
                opcionesFormulario,            
                opcionesFormulario[0]
        );

        if (seleccion == 0) { 
            String usuarioIngresado = cajaNombre.getText().trim(); 
            String contrasenaIngresada = new String(cajaContrasena.getPassword());

            if (profesores.containsKey(usuarioIngresado)) {
                String contrasenaReal = profesores.get(usuarioIngresado);
                
                if (contrasenaReal.equals(contrasenaIngresada)) {
                    JOptionPane.showMessageDialog(null, "¡Bienvenido al sistema de Profesores, " + usuarioIngresado + "!", 
                           "Profesores - Calificaciones", JOptionPane.INFORMATION_MESSAGE);
                    
                    JTextField cajaAlumno = new JTextField(15);
                    JTextField cajaCalificacion = new JTextField(5); 

                    JPanel panelProfesor = new JPanel(new GridLayout(2, 2, 10, 10));
                    panelProfesor.setBorder(BorderFactory.createEmptyBorder(15, 15, 15, 15));

                    panelProfesor.add(new JLabel("Nombre del Alumno:"));
                    panelProfesor.add(cajaAlumno);
                    panelProfesor.add(new JLabel("Calificación (0-10):"));
                    panelProfesor.add(cajaCalificacion);

                    Object[] opcionesProfesor = {"Subir Calificación", "Regresar al Menú"};

                    int seleccion1 = JOptionPane.showOptionDialog(
                            null, 
                            panelProfesor,               
                            "Sistema de Profesores - Escuela Libertad",     
                            JOptionPane.DEFAULT_OPTION, 
                            JOptionPane.PLAIN_MESSAGE, 
                            null, 
                            opcionesProfesor,            
                            opcionesProfesor[0]
                    );

                    if (seleccion1 == 0) {
                        String alumno = cajaAlumno.getText().trim(); 
                        String nota = cajaCalificacion.getText().trim();

                        if (alumno.isEmpty() || nota.isEmpty()) {
                            JOptionPane.showMessageDialog(null, "Por favor, completa todos los campos.", "Error", JOptionPane.ERROR_MESSAGE);
                            abrirMenuB(); 
                        } else {
                            
                            guardarCalificacionEnArchivo(alumno, nota);
                            
                            JOptionPane.showMessageDialog(null, "La calificación de [" + nota + "] fue asignada con éxito a: " + alumno,
                                    "Registro Exitoso", JOptionPane.INFORMATION_MESSAGE);
                            abrirMenuPrincipal();
                        }
                    } else {
                        abrirMenuPrincipal(); 
                    }
                } else {
                    JOptionPane.showMessageDialog(null, "Contraseña incorrecta. Inténtalo de nuevo.", 
                            "Error de Login", JOptionPane.ERROR_MESSAGE);
                    abrirMenuB(); 
                }
            } else {
                JOptionPane.showMessageDialog(null, "El usuario ingresado no existe.", 
                        "Error de Login", JOptionPane.ERROR_MESSAGE);
                abrirMenuB(); 
            }
        } else {
            abrirMenuPrincipal();
        }
    }

    private static Map<String, String> obtenerBaseDeDatosPorfesores() {
        Map<String, String> profesores = new HashMap<>();
        profesores.put("Mariana Toledo Cruz", "CALLIBERTAD1");
        profesores.put("Liliana Andrea Vasquez Caballero", "CALLIBERTAD2");
        return profesores;
    }


    private static ArrayList<String[]> cargarCalificacionesDesdeArchivo() {
        ArrayList<String[]> lista = new ArrayList<>();
        File archivo = new File(ARCHIVO_CALIFICACIONES);

        if (!archivo.exists()) {
            return lista; 
        }

        try (BufferedReader br = new BufferedReader(new FileReader(archivo))) {
            String linea;
            while ((linea = br.readLine()) != null) {
               
                String[] partes = linea.split(",");
                if (partes.length == 2) {
                    lista.add(partes);
                }
            }
        } catch (IOException e) {
            JOptionPane.showMessageDialog(null, "Error al leer el archivo de calificaciones.", "Error", JOptionPane.ERROR_MESSAGE);
        }

        return lista;
    }


    private static void guardarCalificacionEnArchivo(String alumno, String nota) {
        ArrayList<String[]> lista = cargarCalificacionesDesdeArchivo();
        
        boolean actualizado = false;

        for (String[] registro : lista) {
            if (registro[0].equalsIgnoreCase(alumno)) {
                registro[1] = nota;
                actualizado = true;
                break;
            }
        }


        if (!actualizado) {
            lista.add(new String[]{alumno, nota});
        }


        try (PrintWriter pw = new PrintWriter(new FileWriter(ARCHIVO_CALIFICACIONES, false))) {
            for (String[] registro : lista) {
                pw.println(registro[0] + "," + registro[1]);
            }
        } catch (IOException e) {
            JOptionPane.showMessageDialog(null, "Error al guardar en el archivo.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private static void configurarBotonInvisible(JButton boton) {
        boton.setBorderPainted(false);
        boton.setContentAreaFilled(false);
        boton.setFocusPainted(false);
        boton.setCursor(new Cursor(Cursor.HAND_CURSOR)); 
    }

    private static void cerrarVentanaEmergente(JButton boton) {
        Window ventana = SwingUtilities.getWindowAncestor(boton);
        if (ventana != null) {
            ventana.dispose();
        }
    }
