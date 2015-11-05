#!/usr/bin/env python

import argparse
import sys, os
import boto3

import ConfigParser

class Deploy(object):

    def __init__(self):

        global dictionary
        dictionary = {
            'awskey':   '',
            'secret':   '',
            'region':   'us-east-1',
            'profile':  'default',
        }

        parser = argparse.ArgumentParser(
            description='Backup and restore agent for DynamoDB and S3 Buckets',
            usage='''deploy <command> [<sub-command>] [<args>]

The most commonly used commands are:
    list      List the contents of a Dynamo stores, S3 bucket or Regions
    backup    Backup an Dynamo table to an S3 bucket
    restore   Restore an S3 bucket item and place it back in Dynamo
''')

        parser.add_argument('command', help='sub-command to run')

        # parse_args defaults to [1:] for args, but you need to
        # exclude the rest of the args too, or validation will fail
        args = parser.parse_args(sys.argv[1:2])
        if not hasattr(self, args.command):
            print 'Unrecognized command'
            parser.print_help()
            exit(1)

        # use dispatch pattern to invoke method with same name
        getattr(self, args.command)()

    def list(self):
        client = boto3.client('codedeploy')

        parser = argparse.ArgumentParser(description='')
        sublet = parser.add_subparsers(title='sub-commands', description='valid sub-commands', help='additional help')

        parser_tables = sublet.add_argument('--tables', help='list of all available DynamoDB tables in this region')


        # parser_list = sublet.add_argument('-m', help='match a string')
        # parser_list.set_defaults(switch='match')

        # now that we're inside a sub-command, ignore the first TWO
        # argvs, ie the command (transfer) and the sub-command (list)
        args = parser.parse_args(sys.argv[2:])

        response = client.get_application(
            applicationName='DLE'
        )

        print response


        #
        # print conn.list_deployment_groups('DLE')

        #
        #
        # parser_buckets = sublet.add_parser('buckets', help='list of all available S3 buckets in this region')
        # parser_buckets.set_defaults(switch='buckets')
        #
        #
        # if not hasattr(self, args.switch):
        #     print 'Unrecognized command'
        #     parser.print_help()
        #     exit(1)
        #
        # getattr(self, args.switch)()

    def backup(self):

        key, secret = self.getCredentials()
        table  = self.tables(self.regions(True),  True)
        bucket = self.buckets(self.regions(True), True)

        exit(1)

    # AWS Regions
    def regions(self, called = False):
        regions = EC2Connection(dictionary['key'], dictionary['secret']).get_all_regions()

        i = 1
        for region in regions:
            print '%03d' % i, ':', region
            i += 1

        if called:
            regionInput = int(raw_input("\nEnter region number: "))
            if int(regionInput) > len(regions):
                print 'Region %s is not available' % regionInput
                exit(0)

            if regionInput == 0: return '*'

            region = regions[regionInput - 1]

            return region
        else:
            exit(1)

    ## S3 Buckets
    def buckets(self, conn):
        return

    ## Dynamo Tables
    def tables(self, region = False, called = False):
        if region == False:
            region = self.regions(True)

        if not region == '*':
            conn = connect_to_region(aws_access_key_id=dictionary['key'],aws_secret_access_key=dictionary['secret'],region_name=region.name)
        else:
            conn = boto.connect_dynamodb(dictionary['key'], dictionary['secret'])

        tables = conn.list_tables()
        print 'There are %s tables\n' % len(tables)

        i = 1
        tableLength = len(str(len(tables)))
        for table in tables:
            print '%0', tableLength, 'd' % i, ':', table
            i += 1

            if 0 == i % 25:
                tableInput = raw_input('"Enter to continue, enter table number at any time...\n\n')
                if '' != tableInput:
                    break

        if called:
            if 'tableInput' not in locals() or tableInput == '':
                tableInput = int(raw_input("\nEnter table number: "))

            if int(tableInput) > len(tables):
                print 'Table %s is not available' % tableInput
                exit(0)

            print table
            return tables[tableInput - 1]
        else:
            exit(1)

if __name__ == '__main__':
    Deploy()