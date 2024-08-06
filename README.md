# Symlink Manager for ASA Manager

**Note: Create a backup of all servers before making changes!**

This script allows the migration of ARK servers managed by the ASA Manager to a structure using junctions/symlinks.

## Why do I need this?

- **Reduced Storage Usage**: An ARK server occupies about 12 GB of space. Without junctions, each server requires 12 GB. With 6 servers, that’s 72 GB. Using junctions, all 6 servers together use only 12 GB (plus savegames, backups, etc.).
- **Faster Updates**: Only one update is needed to bring all servers up to date.
- **Faster Server Start**: Mods are downloaded during the startup process. Once one server is started, the others do not need to download the mod updates again.

## Requirements

- ASA (Dedicated Server) Manager (https://asadedicatedmanager.eu/)
- Windows PowerShell (Version 5.0 or higher)
- NTFS file system
- Permissions to create junctions/symlinks (administrator rights or equivalent permissions)
- **IMPORTANT**: The servers managed by the ASA Manager must all be in a separate directory (no other directories should be present) - see the "Server Structure" section below.

## Included Scripts

- `migration.ps1` - Migrates from the normal directory structure to the junctions structure.
- `check_migration.ps1` - Checks if the prerequisites are met and if the current user can create junctions.
- `delete_junctions.ps1` (planned) - Restores the original state (checks disk capacity).

## Usage

1. Copy the files `migration.ps1`, `check_migration.ps1`, and `delete_junctions.ps1` to the directory where the ARK servers are installed.
2. Open a PowerShell console (Windows Start button -> Type "PowerShell" -> Start Windows PowerShell).
3. Navigate to the directory containing the servers/maps (see "Server Structure" below).
4. Check if the migration is possible: `./check_migration.ps1`. If the test is successful:
5. Run the migration script: `./migration.ps1`.
6. Choose the desired option:
   - (a) Migrate all servers:
     - Select a starting server. It is recommended to choose the server with the most/largest mods.
     - A new directory "CoreServer" will be created, and the selected server will be copied there (without savegames, etc.).
     - The server files of the other servers/maps will be deleted and replaced by junctions.
   - (b) Migrate a new server:
     - Prerequisite: A CoreServer must already exist.
     - Select the directory of the new server.
     - The migration is performed by deleting the server files and replacing them with junctions. Already migrated servers will be skipped.

**Note:** The CoreServer is NOT directly managed by the ASA Manager. Updates occur within it, but the ASA Manager is not aware of this. The CoreServer allows you to delete and recreate all existing maps with junctions without accidentally deleting the CoreServer.

To undo the migration, use the script `delete_junctions.ps1` (in progress).

## Future server updates
In the future, you will need to shut down all servers in the ASA Manager and update only one (migrated) server. After the update, you can restart the desired servers.
**Important**: The version display in the ASA Manager will only update for each server after it has been started.

## Appendix

### Server Structure

The script assumes a specific server structure. The name of the main directory does not matter, but the structure below it is important. Ensure the structure is correct. Use the ASA Manager if necessary to move or rename servers. Avoid special characters or spaces in the path.

Example structure:
- `c:\GameServer\Ark_ASA_Manager\` # Main directory (the ASA Manager itself)
- `c:\GameServer\Maps\` # Main directory for the servers (this is where the scripts go)
- `c:\GameServer\Maps\TheIsland\` # Directory for a server/map (each server has its own directory)

Additional servers/maps:
- `c:\GameServer\Maps\TheCenter\`
- `c:\GameServer\Maps\Aberration\`
- `c:\GameServer\Maps\ScorchedEarth\`
- `c:\GameServer\Maps\Svartfalheim\`

Each server directory (e.g., `TheIsland`) should contain the following subdirectories:
- `Engine`
- `steamapps`
- `steamcmd`
- `ShooterGame\Binaries`
- `ShooterGame\Content`
- `ShooterGame\Plugins`

If these folders are missing, the structure was not created correctly. Correct this with the ASA Manager.

## Todos
- Cleanly document the code
- Create `delete_junctions.ps1` script

## Important
The scripts and instructions were created using ChatGPT
