# Command class

The mechanism in divided into two parts: an array of `options` and a `parseCSV()` function. Here is the code:


    class ImportationCommand extends ContainerAwareCommand
    {
        // change these options about the file to read
        private $cvsParsingOptions = array(
            'finder_in' => 'app/Resources/',
            'finder_name' => 'fixtures.csv',
            'ignoreFirstLine' => true
        );
    
        protected function execute(InputInterface $input, OutputInterface $output)
        {
            [...]
    
            // use the parseCSV() function
            $csv = $this->parseCSV();
        }
    
        /**
         * Parse a csv file
         * 
         * @return array
         */
        private function parseCSV()
        {
            $ignoreFirstLine = $this->cvsParsingOptions['ignoreFirstLine'];
    
            $finder = new Finder();
            $finder->files()
                ->in($this->cvsParsingOptions['finder_in'])
                ->name($this->cvsParsingOptions['finder_name'])
                ->files()
            ;
            foreach ($finder as $file) { $csv = $file; }
            
            $rows = array();
            if (($handle = fopen($csv->getRealPath(), "r")) !== FALSE) {
                $i = 0;
                while (($data = fgetcsv($handle, null, ";")) !== FALSE) {
                    $i++;
                    if ($ignoreFirstLine && $i == 1) { continue; }
                    $rows[] = $data;
                }
                fclose($handle);
            }
    
            return $rows;
        }
    }
