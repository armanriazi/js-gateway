import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  mockApi,
  mockQueue,
  mockValidation,
  password,
} from '../data/service.js'
import servicesService from '../requiredTest/servicesService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'

export const serviceUpdate = () => {
  describe('UPDATE SERVICE ', () => {
    let adminToken
    let adminId
    let projectId
    let token
    let service2
    let service_upd
    let superAdminId
    let userId
    let service
    let serviceId
    let serviceId2
    let userToken

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }
  
        superAdminId = await usersService.create(superAdminInfo)
  
        delete superAdminInfo.role
  
        token = await usersService.login(superAdminInfo)
  
        const adminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'admin'
        }
  
        adminId = await usersService.create(adminInfo)
  
        delete adminInfo.role
  
        adminToken = await usersService.login(adminInfo)
  
        const userInfo = {
          username: randomUUID(),
          password: password,
          role: 'user'
        }
  
        userId = await usersService.create(userInfo)
  
        delete userInfo.role
  
        userToken = await usersService.login(userInfo)
  
        const project = {
          name: randomUUID(),
        }
  
        projectId = await projectsService.create(project)

        service = {
          apis: mockApi,
          queue: mockQueue,
          validation: mockValidation,
          ProjectId: projectId,
          name: randomUUID(),
        }

        serviceId = await servicesService.create(service)

        service2 = {...service, name : randomUUID()}

        serviceId2 = await servicesService.create(service2)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await servicesService.delete(serviceId2)

      await servicesService.delete(serviceId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
    })

    beforeEach(() => {
      service_upd = {
        apis: mockApi,
        validation: mockValidation,
      }
    })

    it('update service completed successfully', (done) => {
      try {
        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can update services', (done) => {
      try {
        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(service_upd)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        service_upd.api = mockApi

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name must be string', (done) => {
      try {
        service_upd.name = 5464654

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name is duplicate', (done) => {
      try {
        service_upd.name = service.name

        request
          .patch(`/api/services/${serviceId2}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.UNIQUE_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not update a service', (done) => {
      try {
        request
          .delete(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(service_upd)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ServiceId is not uuid', (done) => {
      try {
        let wrongServiceId = 'kjh23j4h2jh34'

        request
          .patch(`/api/services/${wrongServiceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('projectId is not uuid', (done) => {
      try {
        service_upd.ProjectId= 'kjh23j4h2jh34'

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('projectId should not be empty', (done) => {
      try {
        service_upd.ProjectId= ''

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('projectId must be string', (done) => {
      try {
        service_upd.ProjectId= 654564564

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('queue should not be empty', (done) => {
      try {
        service_upd.queue= ''

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.QUEUE} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('queue must be string', (done) => {
      try {
        service_upd.queue= 654564564

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('validation must be an object', (done) => {
      try {
        service_upd.validation = []

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('validation can not be null', (done) => {
      try {
        service_upd.validation = null

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis must be array', (done) => {
      try {
        service_upd.apis = {}

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis can not be null value', (done) => {
      try {
        service_upd.apis = null

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be in array', (done) => {
      try {
        service_upd.apis = [{
          method: 'POST'
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be GET or POST', (done) => {
      try {
        service_upd.apis = [{
          method: ['PATCH']
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.METHOD_VALID_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods can not be empty', (done) => {
      try {
        service_upd.apis = [{
          method: []
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.EMPTY}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods is required', (done) => {
      try {
        service_upd.apis = [{}]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path is required', (done) => {
      try {
        service_upd.apis = [{
          method: ['post']
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name is required', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type is required', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.TYPE} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET is required', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name can not be empty', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': '',
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name must be string', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': {},
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type must be one of image , voice , video', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': '',
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: messages.m.ENTRY_VALID_FILE_TYPE,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type must be string', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': 3564564,
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: messages.m.ENTRY_VALID_FILE_TYPE,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET can not be empty', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'howGet': '',
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ENTRY_VALID_HOWGET}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET must be string', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'howGet': [],
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ENTRY_VALID_HOWGET}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('required file must be boolean', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'howGet': 'file',
            'type': 'image',
            'required': 'tru'
          }]
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path must be string', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: 195.1684525
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path should not be empty', (done) => {
      try {
        service_upd.apis = [{
          method: ['post'],
          path: ''
        }]

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name should not be empty', (done) => {
      try {
        service_upd.name = ''

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token =
          'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .patch(`/api/services/${serviceId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(service_upd)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .patch(`/api/services/${serviceId}`)
          .send(service_upd)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
