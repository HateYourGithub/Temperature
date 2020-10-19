/*
* Copyright (c) 2020 ArtemPopof
*
* This program is free software; you can redistribute it and/or
* modify it under the terms of the GNU General Public
* License as published by the Free Software Foundation; either
* version 2 of the License, or (at your option) any later version.
*
* This program is distributed in the hope that it will be useful,
* but WITHOUT ANY WARRANTY; without even the implied warranty of
* MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
* General Public License for more details.
*
* You should have received a copy of the GNU General Public
* License along with this program; if not, write to the
* Free Software Foundation, Inc., 51 Franklin Street, Fifth Floor,
* Boston, MA 02110-1301 USA
*
* Authored by: Artem Popov <artempopovserg@gmail.com>
*/
public class Application : Gtk.Application {

    private Granite.Widgets.Welcome cpu_temp_label; 

    Application () {
        Object (
            application_id: "com.github.artempopof.temperature",
            flags: ApplicationFlags.FLAGS_NONE
        );
    }

  
    protected override void activate () {
        var main_window = create_main_window ();
        
        var how_to_message = new Granite.Widgets.Welcome ((_("Guess boosted frequency")), (_("Peaking (Bell) EQ filter is being used to boost a certain frequency range. You need to guess boosted frequency. Use the EQ on/off buttons to compare the equalized and non equalized sounds.")));
       
       
        cpu_temp_label = new Granite.Widgets.Welcome (get_cpu_temp (), "");
        //temperature_label.get_style_context ().add_class (Granite.STYLE_CLASS_H1_LABEL);
        
        main_window.add (cpu_temp_label);
        
        main_window.show_all ();
        
        update_badge (to_int_temp (cpu_temp_label.title));
        
        start_update_cycle ();
    }
    
    private Gtk.Window create_main_window () {
        var main_window = new Gtk.ApplicationWindow (this);
        main_window.title = _("CPU Temperature");
        //configure_styles ();

        main_window.default_width = 450;
        main_window.default_height = 450;
        main_window.resizable = false;
        
        Granite.Widgets.Utils.set_color_primary (main_window, {222, 22, 0, 256});
        
        return main_window;
    }
    
    private string get_cpu_temp () {
        string output;
        string error;
        int exit_status;
        Process.spawn_command_line_sync("sensors", out output, out error, out exit_status);
        
        if (exit_status != 0) {
		    stdout.printf("ERROR OCCURED: %s",error);
		    return "Can't determine CPU temperature";
        } else {
            return parse_cpu_temp (output);
        }
    }
    
    private string parse_cpu_temp (string sensors_output) {
        string[] lines = sensors_output.split ("\n");
        
        foreach (var line in lines) {
            if (line.contains ("Core 0:")) {
                return parse_cpu_temp_info (line);
            }
        }
        
        return "Can't determine CPU temperature";
    }
    
    private string parse_cpu_temp_info (string cpu_temp_info_line) {
        string[] lines = cpu_temp_info_line.split ("+");
        
        return "+" + lines[1].split (" ")[0];
    }
    
    private int to_int_temp (string temp_string) {
        string int_string = temp_string.substring (1, temp_string.length - 4);
        stdout.printf("TEMP %s\n", int_string);
        
        return int.parse (int_string.split (".")[0]);
    }
    
    private void update_badge (int temp) {
        stdout.printf("\nupdating badge with value %d", temp);
        Granite.Services.Application.set_badge_visible.begin (true);
        Granite.Services.Application.set_badge.begin (temp);
    }
    
    private void start_update_cycle () {
        var thread = new Thread<int> ("update_temp_thread", this.update_temp);
    }
    
    private int update_temp () {
        stdout.printf ("start updating temp");
        
        while (true) {
            update_badge (to_int_temp (this.cpu_temp_label.title));
            Thread.usleep (1000000);

            this.cpu_temp_label.title = get_cpu_temp ();
        }
        
        return 0;
    }
    
    public static int main (string[] args) {
        var app = new Application ();
        return app.run (args);
    }

}
