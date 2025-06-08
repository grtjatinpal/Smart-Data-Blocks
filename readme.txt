| Part                         | Meaning                                                                    |
| ---------------------------- | ---------------------------------------------------------------------------|
| `register_activation_hook()` | WordPress ko bolna: plugin activate hote hi kuch kaam karna hai            |
| `__FILE__`                   | Plugin file path jahan ye likha gaya hai                                   |
| `'sdb_create_table'`         | Aapka function jo tab chalega jab plugin activate hoga (create Table)      |


| Line                            | Purpose                                                |
| ------------------------------- | ------------------------------------------------------ |
| `global $wpdb;`                 | WordPress DB object use karne ke liye                  |
| `$wpdb->get_charset_collate();` | DB encoding/collation set karne ke liye                |
| `$wpdb->prefix . 'tablename';`  | WordPress-safe table names banane ke liye              |
| `require_once ABSPATH...`       | `dbDelta()` use karne ke liye required file load karna |

// prefix
$table_groups = $wpdb->prefix . 'sdb_groups';
Ye variable aapke custom table ka naam set karta hai — with WordPress table prefix.


// dbDelta()
Aap jab custom table banana chahte ho dbDelta() se (safe way), toh us function ko use karne se pehle uska file include karna padta hai.
Important Restrictions:
dbDelta() sirf CREATE TABLE, ALTER TABLE, DROP INDEX, etc. jaisi SQL commands ke liye use hota hai.
INSERT, SELECT, UPDATE, DELETE ke liye dbDelta() use nahi hota.
Unke liye aap $wpdb->insert(), $wpdb->get_results(), etc. use karte ho.


// database object $wpdb 
$wpdb WordPress ka built-in database access object hai — jisse aap queries likh sakte ho, jaise:
$wpdb->insert()
$wpdb->get_results()
$wpdb->prefix




$sql = "
    CREATE TABLE $table_groups (
        id MEDIUMINT(9) NOT NULL AUTO_INCREMENT,
        name VARCHAR(255) NOT NULL,
        location LONGTEXT NOT NULL,
        key_slug VARCHAR(255) NOT NULL,
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        PRIMARY KEY (id)
    ) $charset_collate;

    CREATE TABLE $table_fields (
        id MEDIUMINT(9) NOT NULL AUTO_INCREMENT,
        group_id MEDIUMINT(9) NOT NULL,
        config LONGTEXT NOT NULL,
        sort_order INT DEFAULT 0,
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        PRIMARY KEY (id),
        INDEX group_id_index (group_id)
    ) $charset_collate;
";































✅ Smart Blocks Plugin — Full Review
🔧 FILE STRUCTURE OVERVIEW
File	Purpose
smart-blocks.php	Main plugin loader – defines constants, loads files
db-schema.php	Creates sdb_groups and sdb_fields tables
ajax.php	Handles dynamic value loading for location dropdowns
class-admin.php	Admin menu + page loading
settings-page.php	Field group (location rules) add/delete interface
settings-fields.php	Manage fields for selected group
class-metaboxes.php	Adds metabox on post edit screen based on location rules
admin.js	Dynamically updates "Location Rules" UI
metaboxes.js	Handles media upload and dynamic repeater UI

✅ WORKING SUMMARY
1. Group Creation (settings-page.php)
Location rules saved as JSON in sdb_groups.location

Post type, page/post, page template rules supported

2. Field Creation (settings-fields.php)
Supports: text, textarea, image, repeater

Repeater supports nested fields

Saved as JSON in sdb_fields.config

3. Metabox Display (class-metaboxes.php)
Dynamically adds metabox based on location rule match

Supports all 4 field types in metabox

Uses wp.media and custom JS to render complex fields

4. Frontend Save (save_post)
Handles sanitize + save for all types

Repeater saves nested field data as JSON

Image saves attachment ID
