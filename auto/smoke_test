#!/bin/bash -e
function test_website {
  website=$1
  echo " "
  echo "Testing:" $website
  response=$(curl --write-out %{http_code} --silent --output /dev/null $website)
  echo "Website status code:" $response

  if [ "$response" -eq "200" ] || [ "$response" -eq "301" ]
    then
      echo "All good with:" $website
      echo "-----------"
  else
      echo "Fix:" $website
      exit 1
  fi
}

test_website "https://pamrucinque.com"
test_website "https://www.pamrucinque.com"
test_website "http://pamrucinque.com"
test_website "http://www.pamrucinque.com"

exit 0