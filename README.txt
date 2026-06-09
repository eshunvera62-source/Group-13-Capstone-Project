=======================================================
  CLIENT DATA - DIRECTORY STRUCTURE GUIDE
=======================================================

ROOT FOLDER: client-data/
  This folder manages all client-related data files
  through their full processing lifecycle.

-------------------------------------------------------
DIRECTORIES
-------------------------------------------------------

📁 incoming/
  Purpose : Holds raw, unprocessed files received from
            clients. Files land here first before any
            validation or review takes place.

📁 archive/
  Purpose : Stores successfully processed files kept
            for long-term record-keeping and auditing.
            Do not delete files from this folder.

📁 rejected/
  Purpose : Contains files that failed validation or
            did not meet required standards. Files here
            need review or to be returned to the client.

📁 reports/
  Purpose : Holds generated reports, summaries, and
            output documents produced from client data.

📁 scripts/
  Purpose : Stores automation and processing scripts
            used to handle, validate, or transform
            files within this client-data workflow.

-------------------------------------------------------
WORKFLOW
-------------------------------------------------------

  incoming/ --> (validate) --> archive/  (success)
                           --> rejected/ (failure)
                --> reports/ (output generated)

=======================================================
  Last Updated : April 2026
=======================================================
